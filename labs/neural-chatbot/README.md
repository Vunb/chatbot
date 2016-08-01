# neural-chatbot
A chatbot based on seq2seq architecture.

### About

This is the successor to my previous torch project 'seq2seq-chatbot'. Portions of this project were directly adapted from the tensorflow translation example.

This is based off the research in these papers: [Sutskever et al., 2014.](http://arxiv.org/abs/1409.3215) and [Vinyals & Le, 2015.](http://arxiv.org/pdf/1506.05869v1.pdf)

### Dependencies

1. Python 2.7
2. [TensorFlow](https://www.tensorflow.org/versions/r0.8/get_started/os_setup.html)
3. (optional) matplotlib `$ pip install matplotlib`

### How to use

#### Getting data

To use this (currently) you will have to provide your own data. This is temporary until I add download functionality to an 
existing online dataset. Your data should be in a format where each succeeding line in a text file is in response to the one
above it. You also should have one text file per conversation. In order to help you choose bucket sizes I've written a script
to plot the sequence lengths of the source and target sequences. To use this do:

`$ python util/sequencelengthplotter.py --data_dir="path/to/raw_data_files"`

The options for the above command are:

|  Name | Type  | Description  |
|:---:|:---:|:---:|
| data_dir  | string  | path to raw data files  |
| plot_histograms  |  boolean | plots histograms for  target and source sequences |
| plot_scatter  | boolean  |  plot x-y scatter plot for target length vs. source length |

in `buckets.cfg` you can then modify the bucket values accordingly. You can add or remove buckets. All bucket values in the [buckets] subheading will be parsed in. Each line under [buckets] should be of the format:

``bucket_name: source_length,target_length``

In the same configuration file the data settings can also be changed under the [max_data_sizes] subheading. 

|  Name | Type  | Description  |
|:--------:|:--------:|:--------:|
| num_lines  | int  | number of lines in conversation to go back  |
| max_target_length  |  int | max length of target sequences |
| max_source_length  | int  |  max length of source sequences |

A configuration file was used because it was a mess trying to find out how to pass bucket values via command line. This seemed like a half-way decent solution. It also enables (in the future) `sequencelengthplotter.py` the ability to autonomously change these values without requiring any user input.

#### Training Network

Once you are satisfied with bucket sizes you can then run the optimizer. This will also clean and process your raw text data. To do this:

`$ python train.py`

There are several options that can be employed:

|   Name               | Type          |     Description                            |
| :-------------------:|:-------------:|:-------------------------------------------|
| hidden_size          | int           | number of hidden units in hidden layers    |
| num_layers           | int           |   number of hidden layers                  |
| batch_size           | int           |    size of batchs in training              |
| max_epoch            | int           |    max number of epochs to train for       |
| learning_rate        | float         |    beggining learning rate                 |
| steps_per_checkpoint | int           |    number of steps before running test set |
| lr_decay_factor      | float         |    factor by which to decay learning rate  |
| batch_size           | int           |    size of batchs in training              |
| checkpoint_dir       | string        |    directory to store/restore checkpoints  |
| dropout              | float         | probability of hidden inputs being removed |
| grad_clip            | int           |    max gradient norm                       |
| max_train_data_size  | int           |    Use a subset of available data to train |
| vocab_size           | int           |    max vocab size                          |
| train_frac           | int           |    percentage of data to use for training (rest is used for testing)   |
| raw_data_dir         | int           |    raw conversation text files stored here |
| data_dir             | int           |    Directory data processor will store train/test/vocab files          |
| is_discrete          | boolean       |    True if your data is discrete pairs of input/output where inputs are not also outputs  |

#### Tensorboard Usage

After training loss and perplexity graphs can be seen:

`$ tensorboard --logdir=/tmp/tb_logs_chatbot/`

#### Sampling output

To have a real conversation with your bot you can begin an interactive prompt by doing:

*Note the `sample.py` file does not currently read in bucket sizes set from the config file (this will be fixed shortly)

`$ python sample.py --checkpoint_dir="path/to/checkpointdirectory" --data_dir="path/to/datadirectory"`

Summary of options below:

|  Name | Type  | Description  |
|:---:|:---:|:---:|
| checkpoint_dir  | string  | path to saved checkpoint  |
| data_dir  |  string | path to directory where vocab file is |

### Results

So far using a Titan X, after about 12 hours of training, it achieves a perplexity of ~30 on a 'relatively small' network. 
Results with human testing so far haven't been too great. I am trying to find the right set of parameters to get something 'ok', 
that can be trained in 24 hours or less on my GPU. Results will be added here when found.

### Future Features

-Automatic bucket selection <s>and making it easier to change bucket sizes</s>

-Automatic download and preparation of an existing conversational dataset
