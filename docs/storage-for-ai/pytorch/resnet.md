实现ResNet50：

```python
import torch
from torch import nn
from torch.utils.data import DataLoader
from torchvision import datasets, models
from torchvision.transforms import ToTensor


def train_loop(dataloader, model, loss_fn, optimizer):
    size = len(dataloader.dataset)
    for batch, (X, y) in enumerate(dataloader):
        # Compute prediction and loss
        pred = model(X)
        loss = loss_fn(pred, y)

        # Backpropagation
        optimizer.zero_grad()
        loss.backward()
        optimizer.step()

        if batch % 100 == 0:
            loss, current = loss.item(), batch * len(X)
            print(f"loss: {loss:>7f}  [{current:>5d}/{size:>5d}]")


def test_loop(dataloader, model, loss_fn):
    size = len(dataloader.dataset)
    num_batches = len(dataloader)
    test_loss, correct = 0, 0

    with torch.no_grad():
        for X, y in dataloader:
            pred = model(X)
            test_loss += loss_fn(pred, y).item()
            correct += (pred.argmax(1) == y).type(torch.float).sum().item()

    test_loss /= num_batches
    correct /= size
    print(
        f"Test Error: \n Accuracy: {(100 * correct):>0.1f}%, \
        Avg loss: {test_loss:>8f} \n"
    )


def main():
    epochs = 5
    learning_rate = 1e-3
    batch_size = 64
    data_root = './data/cifar10'

    training_data = datasets.CIFAR10(
        root=data_root,
        train=True,
        download=True,
        transform=ToTensor()
    )
    test_data = datasets.CIFAR10(
        root=data_root,
        train=False,
        download=True,
        transform=ToTensor()
    )
    train_dataloader = DataLoader(training_data, batch_size=batch_size)
    test_dataloader = DataLoader(test_data, batch_size=batch_size)

    model = models.resnet50()
    print(model)
    loss_fn = nn.CrossEntropyLoss()
    optimizer = torch.optim.SGD(model.parameters(), lr=learning_rate)

    for t in range(epochs):
        print(f"Epoch {t+1}\n-------------------------------")
        train_loop(train_dataloader, model, loss_fn, optimizer)
        test_loop(test_dataloader, model, loss_fn)
    print("Done!")


if __name__ == '__main__':
    main()
```

### 参考文献

1.Built-in datasets, https://pytorch.org/vision/stable/datasets.html#built-in-datasets

2.Models And Pre-Trained Weights, https://pytorch.org/vision/stable/models.html

3.Optimizing Model Parameters, https://pytorch.org/tutorials/beginner/basics/optimization_tutorial.html