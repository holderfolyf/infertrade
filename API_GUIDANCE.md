<p align="center"><img src="https://www.infertrade.com/static/media/InferTradeLogo.5c2cc437.svg" alt="InferTrade"/>
</p>

# InferTrade API Guidance


InferTrade.com is a free webtool for evaluating signals (features in ML/AI) to determine their ability to predict financial markets.

This is the guidance for the main API functionality used to support InferTrade.com.

For the underlying Python code used to calculate the trading strategies used on InferTrade.com, please see our open source (Apache 2.0) infertrade package.

## Authentication

An API key is needed to access the InferTrade API. Please contact support@infertrade.com for access.

Once you have an API key, you will need to it to the header of your requests as the keyword argument 'x-api-key'.

The URL for all requests should be: https://prod.api.infertrade.com

## Rate limit

All access is subject to any conditions attached to provision of your API key, such as fair usage.

### Using the InferTrade API
The "api_automation" module contains the "execute_it_api_request" function,
by supplying the function with a request name from the API_GUIDANCE.md file
and your API key it is able to execute any call mentioned in the guidance.


```python
from infertrade.utilities.api_automation import execute_it_api_request

execute_it_api_request( request_name="Get trading rule metadata", 
                        api_key="YourApiKey")
```

Calls that contain data inside of lists ("[]") need you to provide the specified 
data.In this example, the API request ("Get available time series simulation models")
contains two lists and those are : "research_1" and "price"
To supply this data we simply pass the lists inside a dictionary as 
"additional_data"

```python
from infertrade.utilities.api_automation import execute_it_api_request

additional_data = {"price":[0,1,2,3,4,5,6,7,8,9],"research_1":[0,1,2,3,4,5,6,7,8,9]}
execute_it_api_request( request_name="Get available time series simulation models", 
                        api_key="YourApiKey",
                        additional_data = additional_data)
```

The passed data does not have to replace data inside a list, you can replace any
key listed in the JSON body of the request by using the same feature as before.

If you wish to use your own body or header you can do that by passing them to 
the function:

```python
from infertrade.utilities.api_automation import execute_it_api_request

execute_it_api_request( request_name="Get available time series simulation models", 
                        api_key="YourApiKey",
                        request_body = "YourRequestBody",
                        header = "YourHeader")
```

The default headers are set to:
```python
headers = {
    'Content-Type': 'application/json',
    'x-api-key': 'YourApiKey'
}
```

You can also pass a specific Content Type to the function:

```python
from infertrade.utilities.api_automation import execute_it_api_request

execute_it_api_request( request_name="Get trading rule metadata", 
                        api_key="YourApiKey",
                        Content_Type="YourContentType")
```

The default request are executed using the "request" module but if you prefer
using the "http.client" you can use the "selected_module" argument inside
the function call

```python
from infertrade.utilities.api_automation import execute_it_api_request

execute_it_api_request( request_name="Get trading rule metadata", 
                        api_key="YourApiKey",
                        selected_module="http.client")
```

You can also use the "parse_to_csv" function to read data from a csv file either
located on your computer or the InferTrade package:

```python
from infertrade.utilities.api_automation import execute_it_api_request, parse_csv_file

data = parse_csv_file(file_name="File_Name")
additional = {"trailing_stop_loss_maximum_daily_loss": "value",
            "price": data["Column_Name"],
            "research_1": data["Column_Name"]}
response = execute_it_api_request(
            request_name="Get available time series simulation models",
            api_key="YourApiKey",
            additional_data=additional,
            )
print(response.txt)
```

If you are only providing the file name, the function presumes that it is located in
"/infertrade/".

The same functions can be used alongside postman to generate request bodies,
if you set "execute_request" to false in the function parameters it will return
the request body with additional data:

```python
from infertrade.utilities.api_automation import execute_it_api_request, parse_csv_file

data = parse_csv_file(file_location="File_Location")
additional = {"trailing_stop_loss_maximum_daily_loss": "value",
            "price": data["Column_Name"],
            "research_1": data["Column_Name"]}
response = execute_it_api_request(
            request_name="Get available time series simulation models",
            api_key="YourApiKey",
            additional_data=additional,
            execute_request=False
            )
print(response)
```

The result of this will be the request body with "price", "research_1" and 
"trailing_stop_loss_maximum_daily_loss" set to provided data.

# Example Requests
## **Statistics**
Algorithms for calculating strategy performance metrics, such as Sharpe ratio or Granger P-values.

### POST Get available statistics

<details>
<summary>Example request</summary>

```
import requests
import json

url = "https://prod.api.infertrade.com/"

payload = json.dumps({
  "service": "privateapi",
  "endpoint": "/",
  "session_id": "session_id",
  "payload": {
    "library": "reducerlib",
    "api_method": "get_names_as_dict"
  }
})
headers = {
  'Content-Type': 'application/json',
  'x-api-key': 'InsertYourApiKeyHere'
}

response = requests.request("POST", url, headers=headers, data=payload)

print(response.text)
```
</details>

### POST Get statistics metadata

<details>
<summary>Example request</summary>

```
import requests
import json

url = "https://prod.api.infertrade.com/"

payload = json.dumps({
  "service": "privateapi",
  "endpoint": "/",
  "session_id": "session_id",
  "payload": {
    "library": "reducerlib",
    "api_method": "statistics_details",
    "kwargs": {
      "list_of_names": [
        "SharpeRatio"
      ]
    }
  }
})
headers = {
  'Content-Type': 'application/json',
  'x-api-key': 'InsertYourApiKeyHere'
}

response = requests.request("POST", url, headers=headers, data=payload)

print(response.text)
```
</details>

### POST Calculate statistics

<details>
<summary>Example request</summary>

```
import requests
import json

url = "https://prod.api.infertrade.com/"

payload = json.dumps({
  "service": "privateapi",
  "endpoint": "/",
  "session_id": "session_id",
  "payload": {
    "library": "reducerlib",
    "api_method": "algo_calculate",
    "kwargs": {
      "algorithms": [
        {
          "name": "SharpeRatio"
        },
        {
          "name": "PriceBasicStatistics"
        }
      ],
      "inputs": [
        {
          "time_series": [
            {
              "portfolio_return": [
                35,
                35,
                37,
                39,
                25,
                26,
                27,
                28,
                25,
                26,
                27,
                28,
                25,
                26,
                27,
                28,
                40
              ],
              "allocation": [
                35,
                35,
                37,
                39,
                25,
                26,
                27,
                28,
                25,
                26,
                27,
                28,
                25,
                26,
                27,
                28,
                40
              ],
              "position": [
                35,
                35,
                37,
                39,
                25,
                26,
                27,
                28,
                25,
                26,
                27,
                28,
                25,
                26,
                27,
                28,
                40
              ],
              "price_1": [
                25,
                26,
                27,
                28,
                25,
                26,
                27,
                28,
                25,
                26,
                27,
                28,
                25,
                26,
                27,
                28,
                29
              ],
              "research_1": [
                25,
                26,
                27,
                28,
                25,
                26,
                27,
                28,
                25,
                26,
                27,
                28,
                25,
                26,
                27,
                28,
                29
              ]
            }
          ]
        },
        {
          "time_series": [
            {
              "portfolio_return": [
                35,
                35,
                37,
                39,
                40
              ],
              "allocation": [
                35,
                35,
                37,
                39,
                40
              ],
              "position": [
                35,
                35,
                37,
                39,
                40
              ],
              "price_1": [
                35,
                35,
                37,
                39,
                40
              ],
              "research_1": [
                123,
                26123,
                273,
                2833,
                249
              ]
            }
          ]
        }
      ]
    }
  }
})
headers = {
  'Content-Type': 'application/json',
  'x-api-key': 'InsertYourApiKeyHere'
}

response = requests.request("POST", url, headers=headers, data=payload)

print(response.text)
```
</details>

## **Trading Rules**
### POST Get trading rule metadata

<details>
<summary>Example request</summary>

```
import requests
import json

url = "https://prod.api.infertrade.com/"

payload = json.dumps({
  "service": "privateapi",
  "endpoint": "/",
  "session_id": "session_id",
  "payload": {
    "method": "get_trading_static_info"
  }
})
headers = {
  'Content-Type': 'application/json',
  'x-api-key': 'InsertYourApiKeyHere'
}

response = requests.request("POST", url, headers=headers, data=payload)

print(response.text)
```
</details>

### POST Get available rule representations

<details>
<summary>Example request</summary>

```
import requests
import json

url = "https://prod.api.infertrade.com/"

payload = json.dumps({
  "service": "privateapi",
  "endpoint": "/",
  "session_id": "session_id",
  "payload": {
    "library": "tradingrulelib",
    "api_method": "get_available_representation_types"
  }
})
headers = {
  'Content-Type': 'application/json',
  'x-api-key': 'InsertYourApiKeyHere'
}

response = requests.request("POST", url, headers=headers, data=payload)

print(response.text)
```
</details>

### POST Get rule representation (model, docs, links)

<details>
<summary>Example request</summary>

```
import requests
import json

url = "https://prod.api.infertrade.com/"

payload = json.dumps({
  "service": "privateapi",
  "endpoint": "/",
  "session_id": "session_id",
  "payload": {
    "library": "tradingrulelib",
    "api_method": "algorithm_representation",
    "kwargs": {
      "algorithm_name": "ProportionalWeighting",
      "return_format": "excel",
      "return_type": "url_link"
    }
  }
})
headers = {
  'Content-Type': 'application/json',
  'x-api-key': 'InsertYourApiKeyHere'
}

response = requests.request("POST", url, headers=headers, data=payload)

print(response.text)
```
</details>

## **Time Series Simulation**
### POST Get available time series simulation models

<details>
<summary>Example request</summary>

```
import requests
import json

url = "https://prod.api.infertrade.com/"

payload = json.dumps({
  "service": "privateapi",
  "endpoint": "/",
  "payload": {
    "library": "tradingrulelib",
    "api_method": "calculate_positions_and_returns_v2",
    "kwargs": {
      "function_name": "ConstantPositionSize",
      "market_to_trade": "price",
      "parameters": {
        "trailing_stop_loss_maximum_daily_loss": 0.4
      },
      "df": {
        "price": [
          0.01,
          1,
          2,
          3,
          4,
          5,
          6,
          10,
          8,
          9
        ],
        "research_1": [
          0.01,
          1,
          2,
          3,
          4,
          5,
          6,
          10,
          8,
          9
        ]
      }
    }
  }
})
headers = {
  'Content-Type': 'application/json',
  'x-api-key': 'InsertYourApiKeyHere'
}

response = requests.request("POST", url, headers=headers, data=payload)

print(response.text)
```
</details>

### POST Simulate time series

<details>
<summary>Example request</summary>

```
import requests
import json

url = "https://prod.api.infertrade.com/"

payload = json.dumps({
  "service": "privateapi",
  "endpoint": "/",
  "session_id": "session_id",
  "payload": {
    "library": "generatorlib",
    "api_method": "algo_calculate",
    "kwargs": {
      "algorithms": [
        {
          "name": "StochasticVolatilityWithJumps"
        }
      ],
      "inputs": [
        {
          "random_seed": 12,
          "number_of_price_series": 1,
          "number_of_research_series": 1
        }
      ]
    }
  }
})
headers = {
  'Content-Type': 'application/json',
  'x-api-key': 'InsertYourApiKeyHere'
}

response = requests.request("POST", url, headers=headers, data=payload)

print(response.text)
```
</details>

## **Rule Optimization**
### POST Start a rule optimization

<details>
<summary>Example request</summary>


```
import requests
import json

url = "https://prod.api.infertrade.com/"

payload = json.dumps({
  "service": "privateapi",
  "endpoint": "/",
  "payload": {
    "method": "optimise_trading_rule",
    "args": {
      "scheme": "Global",
      "random_seed": 10,
      "trading_rule_name": "ConstantPositionSize",
      "fixed_parameters": {
        "minimum_allocation_adjustment_size": 0.9462
      },
      "starting_parameters": {},
      "series": [
        {
          "price": [
            0.348465,
            0.33206,
            0.282745,
            0.321925,
            0.2901,
            0.25105,
            0.25166,
            0.20172,
            0.21829,
            0.224165,
            0.197885,
            0.233315,
            0.31734,
            0.28955,
            0.26409,
            0.282515,
            0.284225,
            0.27846,
            0.27334,
            0.284685,
            0.28184,
            0.256165,
            0.26902,
            0.237385,
            0.25652,
            0.25168,
            0.243655,
            0.24334,
            0.261415,
            0.260975,
            0.268145,
            0.312015,
            0.26254,
            0.27435,
            0.29137,
            0.26921,
            0.254175,
            0.241535,
            0.252975,
            0.261245,
            0.25252,
            0.24523,
            0.23021,
            0.24459,
            0.2524,
            0.245225,
            0.251315,
            0.249315,
            0.22894,
            0.227675,
            0.224765,
            0.192575,
            0.176425,
            0.189195,
            0.19037,
            0.17964,
            0.17532,
            0.14781,
            0.16905,
            0.178405,
            0.163885,
            0.19265,
            0.18583,
            0.198845,
            0.19642,
            0.191955,
            0.17238,
            0.1722,
            0.173895,
            0.16165,
            0.164055,
            0.16244,
            0.164405,
            0.177505,
            0.1722,
            0.17629,
            0.17369,
            0.184305,
            0.18107,
            0.179115,
            0.192685,
            0.182415,
            0.18068,
            0.17855,
            0.17281,
            0.16591,
            0.16741,
            0.156915,
            0.158105,
            0.15953,
            0.156455,
            0.154125,
            0.16014,
            0.197605,
            0.235795,
            0.263215,
            0.217025,
            0.21661,
            0.21085,
            0.201685,
            0.2259,
            0.21851,
            0.2289,
            0.25832,
            0.25398,
            0.22166,
            0.228615,
            0.20786,
            0.21492,
            0.23034,
            0.228545,
            0.2107,
            0.21348,
            0.217475,
            0.217645,
            0.21072,
            0.200855,
            0.17406,
            0.185995,
            0.18292,
            0.18169,
            0.19289,
            0.18451,
            0.18408,
            0.17171,
            0.1722,
            0.17733,
            0.17655,
            0.185635,
            0.192785,
            0.210195,
            0.20286,
            0.1967,
            0.1996,
            0.20242,
            0.203655,
            0.202405,
            0.21239,
            0.239195,
            0.235075,
            0.240865,
            0.276465,
            0.246235,
            0.257695,
            0.26204,
            0.253175,
            0.25769,
            0.258705,
            0.261845,
            0.259945,
            0.22505,
            0.216895,
            0.213465,
            0.206655,
            0.20623,
            0.200475,
            0.19245,
            0.206085,
            0.202265,
            0.20163,
            0.200475,
            0.19846,
            0.2025,
            0.20166,
            0.199235,
            0.191385,
            0.19322,
            0.207885,
            0.20225,
            0.199925,
            None,
            0.20143,
            0.205425,
            0.21721,
            0.216335,
            0.202935,
            0.20842,
            0.190355,
            0.199995,
            0.204765,
            0.2086,
            0.22653,
            0.224045,
            0.227535,
            0.232705,
            0.241085,
            0.232075,
            0.23833,
            0.23871,
            0.24075,
            None,
            0.24545,
            0.246765,
            0.279405,
            0.235355,
            0.23489,
            0.24827,
            0.243895,
            0.24494,
            0.24639,
            0.231865,
            0.21536,
            0.20628,
            0.233895,
            0.235345,
            0.22828,
            0.248175,
            None,
            0.457045,
            0.859925,
            0.735645,
            0.739295,
            0.710135,
            0.75441,
            0.728645,
            0.712995,
            1.12035,
            None,
            None,
            None,
            None,
            None,
            None,
            None,
            None,
            None,
            1.96455,
            2.0462,
            2.18615,
            2.73065,
            2.75365,
            2.5057,
            2.6477,
            2.7581,
            2.38485,
            2.0778,
            1.95235,
            1.924,
            2.0128,
            1.9861,
            1.8202,
            1.66095,
            1.14135,
            1.3035,
            1.53325,
            1.53405,
            1.5583,
            1.3632,
            1.3421,
            1.3299,
            1.30165,
            1.21235,
            1.21175,
            1.37305,
            1.2688,
            1.09965,
            1.12695,
            0.968245,
            0.891705,
            0.952625,
            0.82215,
            0.676985,
            0.76626,
            0.718595,
            0.779795,
            0.909415,
            1.0342,
            0.96287,
            1.0413,
            0.985895,
            1.1277,
            1.1139,
            1.11005,
            1.17295,
            1.07685,
            1.1079,
            1.02105,
            0.951335,
            0.8933,
            0.944205,
            0.90207,
            0.90019,
            0.925615,
            0.93112,
            0.883065,
            0.9159,
            0.89758,
            0.89822,
            0.998355,
            0.94443,
            0.902195,
            0.85012,
            0.807995,
            0.83133,
            0.77774,
            0.81357,
            0.784495,
            0.7752,
            0.680675,
            0.68393,
            0.688725,
            0.639185,
            0.646405,
            0.68355,
            0.701855,
            0.672395,
            0.649105,
            0.62792,
            0.63614,
            0.63768,
            0.598685,
            0.579365,
            0.57413,
            0.520015,
            0.49148,
            0.500965,
            0.47925,
            0.49032,
            0.53625,
            0.48955,
            0.49043,
            0.467805,
            0.48562,
            0.500705,
            0.481765,
            0.48818,
            0.509265,
            0.6486,
            0.64063,
            0.64716,
            0.69146,
            0.65568,
            0.6556,
            0.71705,
            0.76269,
            0.921095,
            0.857705,
            0.877725,
            0.876055,
            0.94213,
            0.814025,
            0.8404,
            0.824675,
            0.86303,
            0.869555,
            0.81981,
            0.829265,
            0.865405,
            0.874655,
            0.89714,
            0.90538,
            0.861545,
            0.81925,
            0.80871,
            0.80001,
            0.76864,
            0.678985,
            0.684015,
            0.73507,
            0.72559,
            0.683055,
            0.69267,
            0.661985,
            0.673175,
            0.669395,
            0.695175,
            0.672935,
            0.63725,
            0.60439,
            0.624005,
            0.594275,
            0.60326,
            0.60427,
            0.552235,
            0.593755,
            0.592375,
            0.604185,
            0.6143,
            0.640455,
            0.66358,
            0.662415,
            0.67169,
            0.67284,
            0.675445,
            0.674025,
            0.66387,
            0.55872,
            0.58535,
            0.554005,
            0.526295,
            0.550595,
            0.531625,
            0.53668,
            0.5285,
            0.539965,
            0.54659,
            0.53719,
            0.53501,
            0.48248,
            0.4874,
            0.477235,
            0.47884,
            0.46466,
            0.472105,
            0.45092,
            0.45469,
            0.457965,
            0.462545,
            0.49182,
            0.486,
            0.49534,
            0.47668,
            0.473155,
            0.47131,
            0.48619,
            0.47791,
            0.447375,
            0.448865,
            0.430575,
            0.434755,
            0.437515,
            0.447455,
            0.474975,
            0.509595,
            0.48379,
            0.47783,
            0.444615,
            0.453985,
            0.45053,
            0.445145,
            0.45938,
            0.463925,
            0.449445,
            0.45849,
            0.453695,
            0.4524,
            0.44599,
            0.43352,
            0.44161,
            0.432175,
            0.441845,
            0.428595,
            0.432895,
            0.406575,
            0.379045,
            0.329015,
            0.347495,
            0.31602,
            0.309815,
            None,
            0.27579,
            0.26509,
            0.28652,
            0.291815,
            0.37024,
            0.325625,
            0.343785,
            0.324185,
            0.333995,
            0.318535,
            0.325185,
            0.328305,
            0.326475,
            0.321795,
            0.330675,
            0.351555,
            0.347045,
            0.333235,
            0.335005,
            0.348315,
            0.341525,
            0.337175,
            0.331825,
            0.288325,
            0.30062,
            0.289545,
            0.275175,
            0.274815,
            0.264225,
            0.26486,
            0.26886,
            0.2809,
            0.279775,
            0.281335,
            0.280995,
            0.270905,
            0.322575,
            0.323065,
            0.47067,
            0.583565,
            0.563465,
            0.57597,
            0.502685,
            0.502325,
            0.51831,
            0.540695,
            0.538005,
            None,
            0.585155,
            0.579705,
            0.53904,
            0.524625,
            0.532015,
            0.52056,
            0.482995,
            0.475285,
            0.4925,
            0.484435,
            0.464505,
            0.395205,
            0.422695,
            0.423155,
            0.422755,
            0.45954,
            0.478835,
            0.484225,
            0.465925,
            None,
            0.469885,
            0.465965,
            0.460145,
            None,
            0.467145,
            0.466885,
            0.466385,
            0.460705,
            None,
            0.445075,
            0.445845,
            0.454645,
            0.459415,
            0.462745,
            0.45761,
            0.469085,
            0.498495,
            0.55164,
            0.54016,
            0.49547,
            0.503815,
            0.509325,
            0.510395,
            0.52513,
            None,
            0.481705,
            0.49176,
            0.476825,
            0.496995,
            0.515535,
            0.487455,
            0.43865,
            0.45577,
            0.431785,
            0.414265,
            0.38233,
            0.38375,
            None,
            0.365635,
            0.39616,
            0.381085,
            0.364205,
            0.37777,
            0.37051,
            0.35084,
            0.352805,
            0.34125,
            0.310395,
            0.304935,
            0.312985,
            0.317565,
            0.307705,
            0.303575,
            0.31179,
            0.303565,
            0.292985,
            0.291,
            0.292645,
            0.336765,
            0.358695,
            0.36033,
            0.38722,
            0.366945,
            0.373075,
            0.37905,
            0.41506,
            0.39063,
            0.38804,
            0.345925,
            0.386365,
            0.372435,
            0.381245,
            0.359945,
            0.375355,
            0.388315,
            None,
            0.36841,
            0.361415,
            0.37701,
            0.372195,
            None,
            0.376925,
            None,
            0.33495,
            0.335675,
            0.320855,
            0.339985,
            0.331265,
            0.333585,
            0.334495,
            0.3275,
            0.335345,
            0.32136,
            0.321935,
            0.323495,
            0.32011,
            0.322655,
            0.31955,
            0.318745,
            0.31058,
            0.29805,
            0.29293,
            0.32758,
            0.31476,
            0.31468,
            0.31802,
            0.305485,
            0.301415,
            0.30315,
            0.293825,
            0.295115,
            0.31692,
            0.31762,
            0.31505,
            0.30667,
            0.310345,
            0.308895,
            0.30646,
            0.30689,
            0.305275,
            0.30791,
            0.328395,
            0.329155,
            0.337555,
            0.32519,
            0.329485,
            0.340635,
            None,
            0.335495,
            0.32396,
            0.31752,
            0.32053,
            0.32316,
            0.32144,
            0.317325,
            0.309475,
            0.32252,
            0.32368,
            0.3204,
            0.31382,
            0.319945,
            0.320055,
            0.317445,
            0.317445,
            0.320105,
            0.316515,
            0.321185,
            0.32625,
            0.32284,
            0.32036,
            0.32294,
            0.32446,
            0.31509,
            0.31463,
            0.315495,
            0.311685,
            0.306185,
            0.303215,
            0.3122,
            0.310875,
            0.313005,
            0.31671,
            0.313595,
            0.31717,
            0.352345,
            0.35217,
            0.332485,
            0.367395,
            0.355725,
            0.367725,
            0.361425,
            0.351005,
            0.35292,
            0.328305,
            0.327565,
            0.329655,
            0.331415,
            0.318855,
            0.325485,
            0.33891,
            0.338935,
            0.333005,
            0.32899,
            0.323395,
            0.327435,
            0.322835,
            0.301725,
            0.29171,
            0.30719,
            0.305285,
            0.30917,
            0.304855,
            0.325635,
            0.31631,
            0.31556,
            0.321135,
            0.317335,
            0.31473,
            0.319255,
            0.314455,
            0.310485,
            0.298015,
            0.30091,
            0.328115,
            0.31083,
            0.324105,
            0.39776,
            0.46125,
            0.417495,
            0.384805,
            0.375585,
            0.422995,
            0.39639,
            0.399025,
            0.377335,
            0.380275,
            0.389405,
            0.387235,
            0.410015,
            0.428855,
            0.451315,
            0.45087,
            0.42071,
            0.43178,
            0.431935,
            0.443615,
            0.437565,
            0.39355,
            0.40094,
            0.418995,
            0.421535,
            0.41053,
            0.380615,
            0.396595,
            0.39271,
            0.39921,
            0.40214,
            0.40317,
            0.41016,
            0.431435,
            0.45053,
            0.423995,
            0.43566,
            0.431775,
            0.441485,
            0.46737,
            0.477355,
            0.465155,
            0.464995,
            0.453475,
            0.409755,
            0.414615,
            0.425295,
            0.39503,
            0.40258,
            0.39919,
            None,
            0.314465,
            0.296965,
            None,
            0.312955,
            0.31805,
            0.338005,
            0.32901,
            0.319605,
            0.31223,
            0.31526,
            0.31368,
            0.32434,
            0.30995,
            0.30519,
            0.310175,
            0.31869,
            0.31828,
            0.316355,
            0.31166,
            0.315945,
            0.319895,
            0.32346,
            0.30965,
            0.31259,
            0.30756,
            0.29651,
            0.29861,
            0.303335,
            0.30155,
            0.296345,
            0.27028,
            0.266535,
            0.26278,
            0.265915,
            0.28473,
            0.28277,
            0.27553,
            0.265655,
            0.272905,
            0.277305,
            0.27238,
            0.268755,
            0.270065,
            0.268835,
            0.25744,
            0.257615,
            0.255655,
            0.259355,
            0.25769,
            0.262455,
            0.26476,
            0.259275,
            0.256095,
            0.251885,
            0.261775,
            0.26328,
            0.25959,
            0.257755,
            0.254985,
            0.25547,
            0.254905,
            0.263905,
            0.260345,
            0.262505,
            0.288955,
            0.318435,
            0.30201,
            0.291365,
            0.290165,
            0.276905,
            0.27003,
            0.239575,
            0.24756,
            0.24349,
            0.2431,
            0.242075,
            0.241035,
            0.25559,
            0.250995,
            0.25191,
            0.24905,
            0.253305,
            0.253355,
            0.253535,
            0.275645,
            0.278305,
            0.281625,
            0.273385,
            0.268925,
            0.274205,
            0.27922,
            0.29407,
            0.288005,
            0.285605,
            0.30091,
            0.295435,
            0.291675,
            0.29517,
            0.293445,
            0.29069,
            0.269145,
            0.27773,
            0.29803,
            0.292475,
            0.298235,
            0.29968,
            0.303475,
            0.29675,
            0.294705,
            0.29288,
            0.29684,
            0.29232,
            0.30131,
            0.30194,
            0.310195,
            0.29071,
            0.276155,
            0.280195,
            0.27968,
            0.27476,
            0.272015,
            0.27295,
            0.26814,
            0.261475,
            0.262845,
            0.263925,
            0.25286,
            0.25459,
            0.251005,
            0.244185,
            0.232035,
            0.235955,
            0.223595,
            0.219305,
            0.222115,
            0.22503,
            0.225185,
            0.231435,
            0.22727,
            0.226365,
            0.221015,
            0.220975,
            0.2161,
            0.222965,
            0.227215,
            0.228535,
            0.231035,
            0.22522,
            0.223725,
            0.221705,
            0.219335,
            0.221625,
            0.217175,
            0.21844,
            0.20669,
            0.183845,
            0.196825,
            0.189265,
            0.19622,
            0.192315,
            0.19719,
            0.19037,
            0.190905,
            0.18913,
            0.19025,
            0.190555,
            0.19374,
            0.19744,
            0.193495,
            0.19362,
            0.193195,
            0.188175,
            0.19404,
            0.193515,
            0.194325,
            0.222555,
            0.213735,
            0.208005,
            0.203975,
            0.212355,
            0.211505,
            0.2144,
            0.211555,
            0.234035,
            0.232295,
            0.228165,
            0.23815,
            0.242595,
            0.235785,
            0.23339,
            0.2378,
            0.23596,
            0.225255,
            0.222105,
            0.22056,
            0.230875,
            0.231505,
            0.239525,
            0.236175,
            0.24355,
            0.239325,
            0.24219,
            0.252135,
            0.255235,
            0.266805,
            0.27828,
            0.282765,
            0.27934,
            0.277975,
            0.2825,
            0.274925,
            0.28157,
            0.30366,
            0.327005,
            0.334445,
            0.304465,
            0.295605,
            0.28728,
            0.297545,
            0.275495,
            0.272145,
            0.275005,
            0.275145,
            0.28431,
            0.271145,
            0.252475,
            0.229905,
            0.236785,
            0.23778,
            0.230005,
            0.228435,
            0.239795,
            0.23548,
            0.235115,
            0.239565,
            0.2453,
            0.237365,
            0.204815,
            0.208165,
            0.21329,
            0.20892,
            0.155825,
            0.158845,
            0.14897,
            0.152555,
            0.139045,
            0.149745,
            0.146365,
            0.172185,
            0.155455,
            0.15852,
            0.14974,
            0.15736,
            0.163275,
            0.16125,
            0.17182,
            0.18273,
            0.17533,
            0.16391,
            0.1733,
            0.175485,
            0.175115,
            0.180035,
            0.179725,
            0.18183,
            0.17923,
            0.195205,
            0.193805,
            0.20123,
            0.19937,
            0.187865,
            0.187605,
            0.191475,
            0.188695,
            0.186315,
            0.184095,
            0.19195,
            0.190245,
            0.196685,
            0.19202,
            0.182875,
            0.18509,
            0.18839,
            0.195265,
            0.19289,
            0.19397,
            0.195365,
            0.197075,
            0.214305,
            0.226115,
            0.213835,
            0.217195,
            0.22307,
            0.219925,
            0.218365,
            0.21653,
            0.21812,
            0.21874,
            0.220555,
            0.2164,
            0.19726,
            0.191305,
            0.19685,
            0.20147,
            0.20425,
            0.19876,
            0.20019,
            0.20156,
            0.20521,
            0.204445,
            0.201515,
            0.195375,
            0.200995,
            0.19997,
            0.197315,
            0.19487,
            0.194125,
            0.19667,
            0.19884,
            0.197295,
            0.206445,
            0.20379,
            0.207255,
            0.203375,
            0.204235,
            0.204705,
            0.20352,
            0.20347,
            0.203225,
            0.20267,
            0.201925,
            0.202685,
            0.189245,
            0.192765,
            0.19246,
            0.19079,
            0.192695,
            0.191995,
            0.19285,
            0.190225,
            0.18763,
            0.18871,
            0.186435,
            0.189825,
            0.189275,
            0.18355,
            0.181805,
            0.18329,
            0.176255,
            0.17742,
            0.178015,
            0.175325,
            0.176995,
            0.175655,
            0.176765,
            0.177705,
            0.17709,
            0.187515,
            0.18531,
            0.205115,
            0.202845,
            0.198305,
            0.201025,
            0.20081,
            0.198945,
            0.19892,
            0.19781,
            0.19378,
            0.194445,
            0.19969,
            0.199515,
            0.194795,
            0.198885,
            0.202635,
            0.20788,
            0.20526,
            0.21564,
            0.216315,
            0.22736,
            0.23154,
            0.247215,
            0.2453,
            0.256925,
            0.29019,
            0.288625,
            0.30817,
            0.30306,
            0.30118,
            0.302415,
            0.295135,
            0.293885,
            0.28784,
            0.294635,
            0.280895,
            None,
            0.291465,
            0.30184,
            0.300815,
            0.30038,
            0.320625,
            0.30434,
            0.29009,
            0.29047,
            0.28169,
            0.283895,
            0.28506,
            0.288945,
            0.277475,
            0.27724,
            0.261705,
            0.27025,
            0.275025,
            0.282145,
            0.28243,
            0.296545,
            0.277935,
            0.259025,
            0.255375,
            0.232555,
            0.23993,
            0.243175,
            0.23718,
            0.241125,
            0.243755,
            0.243575,
            0.247035,
            0.241965,
            0.24584,
            0.243815,
            0.248715,
            0.252315,
            0.25032,
            0.251315,
            0.245375,
            0.233165,
            0.2335,
            0.224405,
            0.234145,
            0.242745,
            0.242215,
            0.24274,
            0.24687,
            0.242115,
            0.240545,
            0.238535,
            0.233795,
            0.23324,
            0.247355,
            0.250965,
            0.24558,
            0.248625,
            0.252185,
            0.25306,
            0.256865,
            0.25462,
            0.257005,
            0.256475,
            0.24862,
            0.245785,
            0.2404,
            0.24067,
            0.24165,
            0.247855,
            0.244065,
            0.25348,
            0.263085,
            0.25412,
            0.25614,
            0.252735,
            0.248035,
            0.25115,
            0.2463,
            0.242085,
            0.23923,
            0.2407,
            0.23979,
            0.235825,
            0.2395,
            0.23763,
            0.245335,
            0.257645,
            0.24906,
            0.254395,
            0.25044,
            0.253795,
            0.25595,
            0.25471,
            0.264235,
            0.268295,
            0.269825,
            0.287115,
            0.3027,
            0.294295,
            0.302625,
            0.328075,
            0.46368,
            0.44628,
            0.608555,
            0.69629,
            0.643805,
            0.529375,
            0.55883,
            0.623335,
            0.60278,
            0.66986,
            0.615555,
            0.632295,
            0.63142,
            0.55612,
            0.5842,
            0.62021,
            0.60893,
            0.560605,
            0.58348,
            0.5745,
            0.5467,
            0.5036,
            0.511045,
            0.498215,
            0.470625,
            0.566945,
            0.5743,
            0.5831,
            0.57903,
            0.55748,
            0.51705,
            0.45013,
            0.262215,
            0.34701,
            0.315005,
            0.29495,
            0.28416,
            0.24522,
            0.220095,
            0.212425,
            0.219735,
            0.2367,
            0.22169,
            0.22677,
            0.235625,
            0.22594,
            0.25118,
            0.316085,
            0.319385,
            0.327475,
            0.315485,
            0.288905,
            0.291345,
            0.304835,
            0.296085,
            0.279125,
            0.280625,
            0.27761,
            0.28678,
            0.296925,
            0.295115,
            0.268625,
            0.273485,
            0.270995,
            0.274075,
            0.26842,
            0.268645,
            0.25121,
            0.26344,
            0.28387,
            0.423685,
            0.492035,
            0.375025,
            0.372215,
            0.398795,
            0.44805,
            0.451335,
            0.443305,
            0.419805,
            0.453665,
            0.472875,
            0.50475,
            0.52707,
            0.61011,
            0.62777,
            0.595715,
            0.547205,
            0.521585,
            0.536235,
            0.53176,
            0.5664,
            0.515735,
            0.546245,
            0.57022,
            0.47091,
            0.46973,
            0.425095,
            0.42605,
            0.438195,
            0.415535,
            0.446865,
            0.43532,
            0.451455,
            0.483405,
            0.455875,
            0.46242,
            None,
            0.47429,
            0.485325,
            0.4634,
            0.452855,
            0.439945,
            0.46013,
            0.44338,
            0.43706,
            0.461585,
            0.470805,
            0.470295,
            0.47013,
            0.536265,
            0.51792,
            0.551805,
            0.55064,
            0.47364,
            0.511785,
            0.55441,
            0.55569,
            0.545905,
            0.56156,
            0.56428,
            0.56138,
            0.56838,
            0.593965,
            0.58635,
            0.622915,
            0.910445,
            1.04255,
            0.93996,
            1.0606,
            1.04265,
            1.32045,
            1.36725,
            1.391,
            1.76645,
            1.80945,
            1.79295,
            1.6007,
            1.577,
            1.43365,
            1.3298,
            1.3563,
            1.31185,
            1.16115,
            1.12395,
            1.0858,
            1.0224,
            1.31385,
            1.3969,
            1.35575,
            1.39225,
            1.57615,
            1.65265,
            1.556,
            1.56275,
            1.4098,
            1.5907,
            1.6341,
            1.5587,
            1.5819,
            1.5243,
            1.43065,
            1.4509,
            1.38935,
            1.3209,
            1.38175,
            1.5325,
            1.4318,
            1.45995,
            1.6132,
            1.1738
          ]
        }
      ],
      "date indices": {
        "start": None,
        "end": None
      },
      "quasi_antithetic": False
    }
  }
})
headers = {
  'x-api-key': '',
  'Content-Type': 'application/json'
}

response = requests.request("POST", url, headers=headers, data=payload)

print(response.text)
```
</details>

### POST Retrieve optimization results

<details>
<summary>Example request</summary>

```
import requests
import json

url = "https://prod.api.infertrade.com/"

payload = json.dumps({
  "service": "authapi",
  "endpoint": "/user/data/retrieve",
  "payload": {
    "data-id": "{{data_id_constant_position_size}}"
  }
})
headers = {
  'Content-Type': 'application/json',
  'x-api-key': ''
}

response = requests.request("POST", url, headers=headers, data=payload)

print(response.text)
```
</details>

## **GET Check API status**

<details>
<summary>Example request</summary>

```
import requests
import json

url = "https://prod.api.infertrade.com/status"

payload = ""
headers = {
  'Content-Type': 'application/json'
}

response = requests.request("GET", url, headers=headers, data=payload)

print(response.text)
```
</details>