# NaturalSelection

An all-purpose pythonic genetic algorithm, which also has built-in hyperparameter tuning support for neural networks.


## Installation

```
$ pip install naturalselection
```


## Usage

Here is a toy example optimising a pair of numbers with respect to division.

```python
>>> import naturalselection as ns
>>>
>>> Pair = ns.Genus(x = range(1, 10000), y = range(1, 10000))
>>> def division(number):
...   return number.x / number.y
...
>>> pairs = ns.Population(genus = Pair, size = 10, fitness_fn = division)
>>> history = pairs.evolve(generations = 100, progress_bars = 1)
Evolving population: 100%|███████████████████| 100/100 [00:19<00:00,  5.03it/s]
>>>
>>> history.fittest
{'genome': {'x': 9527, 'y': 190}, 'fitness': 50.142105263157895}
>>>
>>> history.plot()
```

![Alt](https://filedn.com/lRBwPhPxgV74tO0rDoe8SpH/naturalselection_data/example1.png "Optimising pairs of numbers")


Here is an example of finding a feed-forward neural network to model [MNIST](https://en.wikipedia.org/wiki/MNIST_database).

```python
>>> import naturalselection as ns
>>> from tensorflow.keras.utils import to_categorical
>>> import mnist
>>>
>>> # Standard train and test sets for MNIST
>>> X_train = ((mnist.train_images() / 255) - 0.5).reshape((-1, 784))
>>> Y_train = to_categorical(mnist.train_labels())
>>> X_val = ((mnist.test_images() / 255) - 0.5).reshape((-1, 784))
>>> Y_val = to_categorical(mnist.test_labels())
>>>
>>> fitness_fn = ns.get_nn_fitness_fn(
...   kind = 'fnn',
...   train_val_sets = (X_train, Y_train, X_val, Y_val),
...   loss_fn = 'binary_crossentropy',
...   score = 'accuracy',
...   output_activation = 'softmax',
...   max_training_time = 60
...   )
>>>
>>> fnns = ns.Population(
...   genus = ns.FNN(),
...   fitness_fn = fitness_fn,
...   size = 50
...   )
>>> history = fnns.evolve(generations = 10)
Evolving population: 100%|██████████████████| 10/10 [2:20:14<00:00, 699.72s/it]
Computing fitness for generation: 100%|████████| 23/23 [06:57<00:00, 20.05s/it]
>>> 
>>> history.fittest
{'genome': {'optimizer': 'nadam', 'hidden_activation': 'relu',
'batch_size': 256, 'initializer': 'glorot_uniform', 'input_dropout': 0.2,
'layer0': 64, 'dropout0': 0.0, 'layer1': 256, 'dropout1': 0.0, 'layer2': 256,
'dropout2': 0.1, 'layer3': 128, 'dropout3': 0.0, 'layer4': 32, 'dropout4': 0.2}
, 'fitness': 0.9646}
>>> 
>>> history.plot(
...   title = "Average accuracy by generation",
...   ylabel = "Average accuracy"
...   )
```

![Alt](https://filedn.com/lRBwPhPxgV74tO0rDoe8SpH/naturalselection_data/example2.png "Hyperparameter tuning for MNIST")


## License

This project is licensed under the [MIT License](https://github.com/saattrupdan/naturalselection/blob/master/LICENSE).
