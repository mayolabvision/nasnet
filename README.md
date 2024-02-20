# NASNet 

## Example instructions
To demonstrate how to train a network ([Issar et al. 2020](https://doi.org/10.1152/jn.00641.2019)) we have included example waveforms in the file `trainingDataSample.mat` within a folder called `training_data` (this data includes 5000 waveforms from Utah array recordings implanted in a rhesus macaque). This file includes a variable called `waveData` that contains a 5000x53 array (5000 waveforms, the first column is a binary classification- 0 for noise; 1 for spike, the remaining 52 columns are the waveform voltage values).

To demonstrate how to use a trained network to classify waveforms we have included some additional example waveforms in the file `sampleData.mat` (this data comes from 5 minutes of recordings from two Utah arrays). This file includes a variable called `waveforms` that is a 469,234x52 array with 469,234 waveforms that each have 52 samples. We have also included the trained network weights and biases from Issar et al. (2020). 

The network uses TensorFlow and Keras, so it can only be trained in Python. However, the trained network weights are saved as text files and the network is implemented in MATLAB to classify waveforms.  
 
## Training a network (Python only)
1. Download the NASNet folder from GitHub (using recursive strategy to ensure submodules are downloaded): 
```
git clone https://github.com/SmithLabNeuro/nasnet.git --recursive
```
2. Install the necessary software/libraries.
  - Note: We have only tested our code’s compatibility with the following versions:

| Package     | Version |
| ----------- | ------- |
| Python3     | 3.7.4   |
| pip3        | 19.2.3  |
| virtualenv  | 16.7.5  |
| tensorflow  | 2.0     |

  - To install, follow Steps 1-3 of the instructions from [TensorFlow](https://www.tensorflow.org/install/pip). Note: These instructions have you install tensorflow within a virtual environment. It’s easiest if you create the virtual environment within the NASNet folder you downloaded. Otherwise, just make sure to include the NASNet.py file in whatever folder you created the virtual environment in.  
  - Install Keras using the following line in terminal or command prompt: `pip install Keras`
    - Note: If you installed TensorFlow within a virtual environment you must activate that virtual environment (`source ./venv/bin/activate`) and then install Keras within it

3. Train a network
  - The `NASNet.py` file contains a function called `NASNet.trainnnet`. This function has 3 required input arguments. The first is the name of your network (ex. `SampleNetwork`), the second is the number of time points in each waveform (ex. 52), and the third is the name of the folder containing the training data (`training_data`). 
Each training data file must contain a variable called `waveData` that contains an N x (1+n) matrix (N is the # of waveforms, n is the number of time/sample points in each waveform).
    - The rows are the N waveforms to train the model with.
    - The first column of the matrix should be the BINARY waveform label (0 for noise, 1 for spike) for each waveform
    - The remaining columns are the waveform voltage values 
    - Make sure the .mat file is saved using the `-v7.3` flag	

  - Type the following line into terminal/command prompt:
    ```
    python -c ‘import NASNet; NASNet.trainnet(“SampleNetwork”,52, “training_data”)’
    ```
  - The output of running the network will include 6 files. There will be a .txt file with the weights (ex. `SampleNetwork_w_hidden`) and a .txt file with the biases (ex. `SampleNetwork_b_hidden`) of the hidden layer, and the corresponding files for the output layer (`SampleNetwork_w_output`, `SampleNetwork_b_output`). These four outputs are what will be required to implement the network in MATLAB in section II of these instructions. The fifth output is the trained network in the form of a Keras model (this can be opened and manipulated using Keras functions in Python). The sixth output is a .txt file that just lists the training directory used to generate the network you just created. 

## Classifying waveforms with the network (Matlab)
The trained network can classify waveforms that are the same length as the waveforms it was trained on. A network trained on waveforms that were 52 samples long cannot classify waveforms that are 30 samples long unless they are first up-sampled to 52 samples.
1. In Matlab, use the function `runNASNet` to classify waveforms in a .nev file or from a .mat file. The function requires the filename, gamma threshold, and network name. If the input is a .nev file the function uses the function `moveToSortCode` to modify the nev file with the new spike classifications. 
  - See the [wrapper](Issar_et_al_2020_wrapper.m) file for an example of using the function.
