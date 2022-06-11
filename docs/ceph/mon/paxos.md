Multi Paxos
两类节点：
* Leader
* Peon

```c
// leader
void Paxos::begin(bufferlist& v)
{
  // accept it ourselves
  accepted.clear();
  accepted.insert(mon.rank);
  new_value = v;

  if (last_committed == 0) {
    auto t(std::make_shared<MonitorDBStore::Transaction>());
    // initial base case; set first_committed too
    t->put(get_name(), "first_committed", 1);
    decode_append_transaction(t, new_value);

    bufferlist tx_bl;
    t->encode(tx_bl);

    new_value = tx_bl;
  }
  
  if (mon.get_quorum().size() == 1) {
    // we're alone, take it easy
    commit_start();
    return;
  }
  
  // ask others to accept it too!
  for (auto p = mon.get_quorum().begin(); p != mon.get_quorum().end(); ++p) {
    if (*p == mon.rank) continue;

    MMonPaxos *begin = new MMonPaxos(mon.get_epoch(), MMonPaxos::OP_BEGIN,
                                     ceph_clock_now());
    begin->values[last_committed+1] = new_value;
    begin->last_committed = last_committed;
    begin->pn = accepted_pn;
    mon.send_mon_message(begin, *p);
  }
}
```



```c
// peon
void Paxos::handle_begin(MonOpRequestRef op)
{
  op->mark_paxos_event("handle_begin");
  auto begin = op->get_req<MMonPaxos>();

  // can we accept this?
  if (begin->pn < accepted_pn) {
    op->mark_paxos_event("have higher pn, ignore");
    return;
  }

  // set state.
  state = STATE_UPDATING;
  lease_expire = {};  // cancel lease

  // yes.
  version_t v = last_committed+1;

  // reply
  MMonPaxos *accept = new MMonPaxos(mon.get_epoch(), MMonPaxos::OP_ACCEPT,
                                    ceph_clock_now());
  accept->pn = accepted_pn;
  accept->last_committed = last_committed;
  begin->get_connection()->send_message(accept);
}
```

