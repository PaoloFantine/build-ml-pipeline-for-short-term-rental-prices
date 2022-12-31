# Build an ML Pipeline for Short-Term Rental Prices in NYC
You are working for a property management company renting rooms and properties for short periods of 
time on various rental platforms. You need to estimate the typical price for a given property based 
on the price of similar properties. Your company receives new data in bulk every week. The model needs 
to be retrained with the same cadence, necessitating an end-to-end pipeline that can be reused.

In this project you will build such a pipeline.

## Table of contents

- [Introduction](#build-an-ML-Pipeline-for-Short-Term-Rental-Prices-in-NYC)
- [Preliminary steps](#preliminary-steps)
  * [git repository](#git-repository)
  * [Weights and Biases](#weights-and-biases)
  * [Create environment](#create-environment)
  * [The configuration](#the-configuration)
  * [Running the entire pipeline or just a selection of steps](#Running-the-entire-pipeline-or-just-a-selection-of-steps)
  * [Pre-existing components](#pre-existing-components)
- File Structure
- License

## Preliminary steps
### git repository
All necessary code is recorded in [https://github.com/PaoloFantine/build-ml-pipeline-for-short-term-rental-prices.git](https://github.com/PaoloFantine/build-ml-pipeline-for-short-term-rental-prices.git)

### Weights and Biases
All pipeline tracking is recorded in Weights & Biases. The project is publicly accessible at [https://wandb.ai/pfantine/nyc_airbnb?workspace=user-pfantine]https://wandb.ai/pfantine/nyc_airbnb?workspace=user-pfantine

### Create environment
Make sure to have conda installed and ready, then create a new environment using the ``environment.yml``
file provided in the root of the repository and activate it:

```bash
> conda env create -f environment.yml
> conda activate nyc_airbnb_dev
```

### The configuration
As usual, the parameters controlling the pipeline are defined in the ``config.yaml`` file defined in
the root of the starter kit. We will use Hydra to manage this configuration file. 
Open this file and get familiar with its content. Remember: this file is only read by the ``main.py`` script 
(i.e., the pipeline) and its content is
available with the ``go`` function in ``main.py`` as the ``config`` dictionary. For example,
the name of the project is contained in the ``project_name`` key under the ``main`` section in
the configuration file. It can be accessed from the ``go`` function as 
``config["main"]["project_name"]``.

NOTE: do NOT hardcode any parameter when writing the pipeline. All the parameters should be 
accessed from the configuration file.

### Running the entire pipeline or just a selection of steps
In order to run the pipeline when you are developing, you need to be in the root of the starter kit, 
then you can execute as usual:

```bash
>  mlflow run .
```
This will run the entire pipeline.

When developing it is useful to be able to run one step at the time. Say you want to run only
the ``download`` step. The `main.py` is written so that the steps are defined at the top of the file, in the 
``_steps`` list, and can be selected by using the `steps` parameter on the command line:

```bash
> mlflow run . -P steps=download
```
If you want to run the ``download`` and the ``basic_cleaning`` steps, you can similarly do:
```bash
> mlflow run . -P steps=download,basic_cleaning
```
You can override any other parameter in the configuration file using the Hydra syntax, by
providing it as a ``hydra_options`` parameter. For example, say that we want to set the parameter
modeling -> random_forest -> n_estimators to 10 and etl->min_price to 50:

```bash
> mlflow run . \
  -P steps=download,basic_cleaning \
  -P hydra_options="modeling.random_forest.n_estimators=10 etl.min_price=50"
```

### Pre-existing components
In order to simulate a real-world situation, we are providing you with some pre-implemented
re-usable components. While you have a copy in your fork, you will be using them from the original
repository by accessing them through their GitHub link, like:

```python
_ = mlflow.run(
                f"{config['main']['components_repository']}/get_data",
                "main",
                parameters={
                    "sample": config["etl"]["sample"],
                    "artifact_name": "sample.csv",
                    "artifact_type": "raw_data",
                    "artifact_description": "Raw file as downloaded"
                },
            )
```
where `config['main']['components_repository']` is set to 
[https://github.com/udacity/build-ml-pipeline-for-short-term-rental-prices#components](https://github.com/udacity/build-ml-pipeline-for-short-term-rental-prices/tree/main/components).
You can see the parameters that they require by looking into their `MLproject` file:

- `get_data`: downloads the data. [MLproject](https://github.com/udacity/build-ml-pipeline-for-short-term-rental-prices/blob/main/components/get_data/MLproject)
- `train_val_test_split`: segrgate the data (splits the data) [MLproject](https://github.com/udacity/build-ml-pipeline-for-short-term-rental-prices/blob/main/components/train_val_test_split/MLproject)

## File structure
Relevant code is mainly in the folders `src` and `components`. Some files are used to configure
the environment and the parameters for the pipeline to run correctly and avoid hard codings:

- environment.yml: file containing the conda environment needed to run this project
- config.yaml: file storing all parameters needed for the different steps in the pipeline
- conda.yml: file storing the conda environment needed to run the main file
- main.py: script gathering the pipeline steps to be run
- MLproject: file determining the necessary entry points
- components/:
  * get_data/: download the raw data to be run through the pipeline
  * train_val_test_split/: split cleaned data into training and test data
  * test_regression_model/: test the model after it has been trained
- src/:
  * eda/: explore data in a jupyter notebook and determine the needed cleaning steps 
  * basic_cleaning/: clean the data for the pipeline
  * data_check/: test the data cleaning steps for the inference steps
  * train_random_forest/: train the model 

each subfolder of `src/` and `components/` contains the necessary `run.py`, `conda.yml` and `MLproject` for the step to run.
Each step is called in `main.py` and can be skipped or included as described in [Running the entire pipeline or just a selection of steps](#Running-the-entire-pipeline-or-just-a-selection-of-steps).

## License

[License](LICENSE.txt)
