# fastapi_ml_deployment

Deploying ML models using FastAPI for scalability

## File Structure

```{}
fastapi_ml_deployment
├── README.md                       <- The top-level README for users of this site
├── gitignore
├── LICENSE
├── main.py                         <- FastAPI script
├── model.py                        <- script for the classifier class object
├── requirements.txt                <- file listing all required libraries
└── lib/
    ├── data/
    ├── figures/
    |   ├── ^GSPC_plot.png
    |   ├── ^GSPC_plot_components.png
    |   ├── AAPL.png
    |   ├── AAPL_plot_components.png
    |   ├── GOOG_plot.png
    |   ├── GOOG_plot_components.png
    |   ├── MSFT_plot.png
    |   └── MSFT_plot_components.png
    └── models/                     <- joblib models for import into API script
        ├── ^GSPC.joblib
        ├── AAPL.joblib
        ├── GOOG.joblib
        └── MSFT.joblib
```

## Objectives

Provide an example of using FastAPI to predict stock prices using Facebook Prophet forecasting to allow for fast deployment and scaling of ML models

1. Develop a RESTful API with Python and FastAPI
2. Build a basic ML model to predict stock prices using Facebook Prophet
3. Deploy a FastAPI app to Heroku

NOTE: I will continue to add additional models to this project over time

## Project Setup

Create a project folder

```{python}
$mkdir fastapi_ml_deployment
$cd fastapi_ml_deployment
```

Create and activate a new virtual environment (using Miniconda installation)

```{python}
$conda create --name venv python=3.7
$conda activate venv
```

Install required python libraries

```{python}
(venv)$ pip install -r requirements.txt
```

NOTE: If running this code on a Windows machine, you will also need to install the following for fbprophet to install correctly

```{python}
(venv)$ conda install libpython m2w64-toolchain -c msys2
```

Start the app

```{python}
(venv)$ uvicorn main:app --reload --workers 1 --host 0.0.0.0 --port 8008
```

Uvicorn uses the following settings:

1. ```main:app``` tells Uvicorn where it can find the FastAPI ASGI application (e.g., within the 'main.py' file)
2. ```--reload``` enables auto-reload so the server will restart after changes are made to the code base
3. ```--workers 1``` provides a single worker process
4. ```--host 0.0.0.0``` defines the address to host the server on
5. ```--port 8080``` defines the port to host the server on

Navigate to [http://localhost:8008/ping](http://localhost:8008/ping) to test the API is working

Check out [http://localhost:8008/docs](http://localhost:8008/docs) to see and test the FastAPI documentation

## Models

### 1. FB Prophet ML model to predict stock prices

This model has three functions:

1. ```train``` downloads historical stock data using [yfinance](https://pypi.org/project/yfinance/), creates a new FB Prophet model, fits the model to the stock data, and then serializes and saves the model as a joblib file
2. ```predict``` loads and deserializes the saved model, generates a new forecast, creates images of the forecast plot and forecast components, and returns the days included in the forecast as a list of dicts
3. ```convert``` takes the list of dicts from ```predict``` and outputs a dict of dates and forecasted values (i.e., {"2020-07-01": 200})

Test the API endpoint

```{python}
$ curl \
--header "Content-Type: application/json" \
--request POST \
--data '{"ticker": "MSFT"}' \
http://localhost:8008/predict
```

^GSPC
![GSPC_plot](/lib/figures/^GSPC_plot.png)
![MSFT_plot_components](/lib/figures/^GSPC_plot_components.png)

AAPL
![MSFT_plot](/lib/figures/AAPL_plot.png)
![MSFT_plot_components](/lib/figures/AAPL_plot_components.png)

GOOG
![MSFT_plot](/lib/figures/GOOG_plot.png)
![MSFT_plot_components](/lib/figures/GOOG_plot_components.png)

MSFT
![MSFT_plot](/lib/figures/MSFT_plot.png)
![MSFT_plot_components](/lib/figures/MSFT_plot_components.png)
