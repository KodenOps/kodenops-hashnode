---
title: "Data Visualization With Aws Cloudwatch"
datePublished: Sat Aug 02 2025 16:48:08 GMT+0000 (Coordinated Universal Time)
cuid: cmduhkrw9000n02lhegclg65t
slug: data-visualization-with-aws-cloudwatch
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/shr_Xn8S8QU/upload/512161246f9792a72515f75b9746941d.jpeg
tags: aws, apis, monitoring, visualization, observability, cloudwatch

---

![](https://cdn-images-1.medium.com/max/1000/1*W7IYUbQJAVnTsCYeVak5gg.png align="center")

Image: What we’ll be achieving at the end of this article

In my last article, I wrote about the basics of monitoring where I highlighted all that is needed to ensure we are able to create all those shining dashboards we see everywhere. Just briefly, these includes:

1. Data source
    
2. Pipeline for data fetching and transformation
    
3. Visualization tool
    

You can read the article here first before you proceed: [Monitoring Like a pro](https://medium.com/@femifadiya.segun/monitoring-like-a-pro-f3ddbb81d19a)

Monitoring application or resources through visualization on dashboards is critical both because of the ease and swiftness of getting the information and the ability to have a wholistic view of all component at a glance.

In this article, we will be creating visualization that monitor the weather of different countries on a single dashboard. Sounds cool, right?

So, without wasting time, let’s talk about all the important components we need.

For this project, we need to figure out the three data visualization core components

a. Data source: for the data source, we will be using OpenWeather API.

b. Pipeline: For fetching and transformation of data, we will be using python script. This python script will fetch the data, transform the data into the major information we need and then store it in a reservoir. Here we will use AWS S3 service.

c. Visualization tool: To view the weather info, we will use AWS Cloud Watch. On this Cloud Watch, we will create visualization dashboard that updates at interval. This means, it is automated. Once new data enters, the dashboard picks it on refresh.

![](https://cdn-images-1.medium.com/max/1000/1*pKaac6jRKkbzIDu6UxWIKw.png align="left")

Architecture of this project

Roll up your sleeves and let’s get our hands dirty.

### Getting Started

To get started, we’ll need to setup our environment. This involves installation of dependencies and setup of our file structures.

> Installations & Signups

* Download Python on your local environment and ensure the PATH is saved in your environmental variables (for windows users only — linux users should only install)
    
* **Create openweather account:** Proceed to openweather website and create an account. After this, create an API key which will be used in fetching the weather information across different regions. [Link to Openweather](https://openweathermap.org/api)
    

> Setup Of Local environment

* Create a folder/directory called WEATHER\_DASHBOARD (Call it anything, really — who cares)
    
* Open this folder on VsCode
    
* Create src directory in this folder
    
* Inside the directory, create three files called Weather-script.py, **init**.py and .env
    
* From your Openweather portal, copy your API key and paste it in your .env file like this.
    

```plaintext
WEATHER_API_KEY=xxxxxxxxxxxxxxxxxxx
```

* You need a name for your AWS S3 bucket name. Use a very unique name that no one has used on S3 before. Add this to your .env also
    

```plaintext
WEATHER_API_KEY=xxxxxxxxxxxxxxxxxxx
AWS_BUCKET_NAME=weather-bucket-name-for-dashboard-project
```

> Final installations

* Install AWS CLI: Use this link to [download](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
    
* After installation, restart your vscode and type **aws config** in the terminal. If it prompt you for access key, that means it’s successfully installed
    
* Install the following dependencies:
    

— — — **boto3**: this is python library that allow you control aws resources programmatically. Read docs here. Install by typing this line inside vscode terminal right in this project directory:

```bash
pip install boto3
```

— — — **Python-dotenv**: this allow you to use .env and fetch data from it.

```bash
pip install python-dotenv
```

Since we will be fetching data using API, we will need to install a library that will enable that functionality.

— —**Request**: This will let you fetch data from API

```bash
pip install requests
```

Phewwwww! A lot of pip-ing going on here.

* Navigate to IAM console in your AWS profile. Create a new user and assign these permissions (s3:CreateBucket, s3:ListBucket, s3:GetObject, AmazonS3FullAccess, s3:PutObject)
    
* Generate access key and copy the **key\_ID** and **Secret\_Key**
    
* Run this in your teminal
    

```bash
aws configure
```

* Put the ID and Secret key in the prompt individually. Set the export format to json.
    

> Guess what?! We are done with that now.

Now, inside your **Weather-script.py** file, paste this code

```python
import os
import boto3
import requests
import csv
import io
from datetime import datetime
from dotenv import load_dotenv

# Load environment variables
load_dotenv()

class WeatherFetcher:
    # Initialize environment
    def __init__(e):
        e.api_key = os.getenv("WEATHER_API_KEY")
        e.bucket_name = os.getenv("AWS_BUCKET_NAME")
        e.s3_client = boto3.client("s3")

    # Create the S3 bucket if it doesn't exist
    def create_bucket(e):
        '''Check if S3 bucket exists; create if not.'''
        try:
            e.s3_client.head_bucket(Bucket=e.bucket_name)
            print(f"Bucket {e.bucket_name} exists")
        except e.s3_client.exceptions.ClientError:
            print(f"Creating new bucket {e.bucket_name}")
            try:
                e.s3_client.create_bucket(Bucket=e.bucket_name)
                print(f"Successfully created bucket: {e.bucket_name}")
            except Exception as error:
                print(f"Error creating bucket: {error}")

    # Fetch weather data from OpenWeather API
    def fetch_weather(e, city):
        '''Fetch weather data from OpenWeather API.'''
        base_url = "http://api.openweathermap.org/data/2.5/weather"
        params = {
            "q": city,
            "appid": e.api_key,
            "units": "metric"
        }
        try:
            response = requests.get(base_url, params=params)
            response.raise_for_status()
            return response.json()
        except requests.exceptions.RequestException as error:
            print(f"Error fetching weather data: {error}")
            return None

    # Overwrite weather data in S3 CSV file
    def overwrite_to_s3(e, weather_data, city):
        '''Overwrite the weather data in the S3 CSV file.'''
        if not weather_data:
            return False

        timestamp = datetime.utcnow().strftime('%Y-%m-%dT%H:%M:%SZ')  # ISO 8601 format
        file_name = f"weather-data/{city}-weather.csv"

        # Prepare CloudWatch-compatible CSV data
        new_data = {
            "Timestamp": timestamp,  # First column (A)
            "Temperature": weather_data['main']['temp'],  # Numeric value
            "FeelsLike": weather_data['main']['feels_like'],  # Numeric value
            "Humidity": weather_data['main']['humidity']  # Numeric value
        }

        try:
            # Overwrite the S3 file with fresh data
            csv_buffer = io.StringIO()
            writer = csv.DictWriter(csv_buffer, fieldnames=["Timestamp", "Temperature", "FeelsLike", "Humidity"])
            
            # Write the header only once
            writer.writeheader()
            writer.writerow(new_data)  # Write the new data entry

            # Upload the new CSV content to S3
            e.s3_client.put_object(
                Bucket=e.bucket_name,
                Key=file_name,
                Body=csv_buffer.getvalue(),
                ContentType='text/csv'
            )
            print(f"Successfully overwritten the file {file_name} with new data in S3")
            return True
        except Exception as error:
            print(f"Error overwriting file: {error}")
            return False


def main():
    dashboard = WeatherFetcher()

    # Create the bucket if needed
    dashboard.create_bucket()

    # List of cities updated as per your request
    cities = ["China", "Canada", "Nigeria", "India"]

    for city in cities:
        print(f"\nFetching weather for {city}...")
        weather_data = dashboard.fetch_weather(city)
        if weather_data:
            temp = weather_data['main']['temp']
            feels_like = weather_data['main']['feels_like']
            humidity = weather_data['main']['humidity']
            description = weather_data['weather'][0]['description']

            print(f"Temperature: {temp}°C")
            print(f"Feels like: {feels_like}°C")
            print(f"Humidity: {humidity}%")
            print(f"Conditions: {description}")

            # Overwrite data to S3 CSV file
            success = dashboard.overwrite_to_s3(weather_data, city)
            if success:
                print(f"Weather data for {city} overwritten to S3 as CloudWatch-compatible CSV!")
        else:
            print(f"Failed to fetch weather data for {city}")

if __name__ == "__main__":
    main()
```

I’ll assume you know python before now. But if you don’t, what this code did was:

1. import the dependencies needed.
    
2. Initiate the dotenv so that we can proceed to start calling the saved environmental variables.
    
3. Initialize the environment variables and saved them in another variable which will be called during code execution.
    
4. Connect to AWS and create S3 bucket — this is where final .csv file will be saved
    
5. Fetch the weather info from the API
    
6. Finally, it saved the data in a file called **weather-data/{city}-weather.csv**
    

> Run the code

To run the python code, nevigate in yout terminal to the directory where your **Weather-script.py** is located.

type this and run

```plaintext
python ./Weather-script.py
```

This will fetch the data, process it into .csv file and save it in your s3 bucket. Name will be what you put in your .env.

Check you AWS S3 console to see the newly created bucket

![](https://cdn-images-1.medium.com/max/1000/1*jXk828UlrZo8zN_-GM9hBA.png align="left")

You should have something like this with the name you selected in your .env file

Click the name and the sub-directory to see the 4 weather files for the 4 countries.

![](https://cdn-images-1.medium.com/max/1000/1*ubyFv_vsqNeQl-GhUKa4iw.png align="left")

On every run of the script, this file are over-written to ensure latest data are displayed on CloudWatch dashboard

Now we are done with the first part

#### Final stage: Data visualization on CloudWatch

* Navigate to CloudWatch console
    

![](https://cdn-images-1.medium.com/max/1000/1*7d5Yp3vyc5LZ5oPLjC2lpg.png align="left")

* Click **Create a default dashboard**button
    
* Click **Create Dashboard** and give it a name. et’s call it **Lesson-dash**
    
* click the **create datasource**
    

![](https://cdn-images-1.medium.com/max/1000/1*2-Ca2O2QU6dczw4N0kg8Ww.png align="left")

* Search for s3 and select. Then click **Next**
    
* Give your data source a random but unique. Then check the acknowledge the consent
    
* Click **Create Data Source** button
    

![](https://cdn-images-1.medium.com/max/1000/1*0ebLjiyurbsNlZ0ybQsLYw.png align="left")

* Click the **Query from CloudWatch metrics** in front of your newly created data source
    

![](https://cdn-images-1.medium.com/max/1000/1*UaxUSFo2gRic1PQUX8PAWA.png align="left")

* click the s3 Bucket search field and select your bucket
    
* In the Object Key, select your desired .csv file and click the **Graph Query** button
    

![](https://cdn-images-1.medium.com/max/1000/1*Z9NfCucMMo0rr8JCKw7-wQ.png align="left")

* On the top navigations/buttons, click on the line dropdown and change to guage
    

![](https://cdn-images-1.medium.com/max/1000/1*Iu6s6RnZd5CLj3ZMQR0NTg.png align="left")

* Choose Guage (let’s assume humidity, temperature and feels-like have the same range which we will use -100 to +100).
    

![](https://cdn-images-1.medium.com/max/1000/1*oU24hhcqUdgLpDJtUKYHdQ.png align="left")

You should have this now

Now, let’s set the range and thresholds

* click the options button under the chart.
    

![](https://cdn-images-1.medium.com/max/1000/1*CIB59azf0fhZ7hfZ1zZWdA.png align="left")

* set min to -100 and max to 100
    

![](https://cdn-images-1.medium.com/max/1000/1*h4WRyJ8hFs56Sa1E4D6OdA.png align="left")

Click the **Add Thresholds** to set colors based of different metric

Here is what we will use:

* 100–0 = blue — — → freezing
    
* 0–32 = Green — → Okay
    
* \&gt;32 = Red — — → Too hot
    

![](https://cdn-images-1.medium.com/max/1000/1*RpyJQ2v1qtnXuVxf8IXYtQ.png align="left")

* Now rename the panel (remember, we picked the Canada .csv) from **Untitled graph** to Canada metrics and save.
    

![](https://cdn-images-1.medium.com/max/1000/1*mcOkR9f4OHrwbSV8wNWjIQ.png align="left")

* Now, locate the Action button at the top and select **Add to Dashboard — Improved.**
    

![](https://cdn-images-1.medium.com/max/1000/1*ibWGNue1r6rtVTzcurK9yQ.png align="left")

* Choose the dashboard you created earlier and click **Add to dashboard** button. Adjust the panel to show all the metrics and click **Save Dashboard button**
    

![](https://cdn-images-1.medium.com/max/1000/1*Fq-MU9xweUIAJuTroVa9Qw.png align="left")

* click the three dots at on the top-right corner of the metric panel and select duplicate
    

![](https://cdn-images-1.medium.com/max/1000/1*EYNyQmO5wKvF481cZhrhuA.png align="left")

On the new panel, click the three dots again and this time, select **Edit**

![](https://cdn-images-1.medium.com/max/1000/1*wkUewgugPfrfIsZBi0ujgA.png align="left")

select the pen icon after the LAMBDA in Details section

![](https://cdn-images-1.medium.com/max/1000/1*WpGHO4zhYyPy3p52Y7F9mA.png align="left")

Select **Edit from Editor,** and pick another country .csv file (Nigeria)

![](https://cdn-images-1.medium.com/max/1000/1*_dUFDFdWhHVkYlKgCWRP-A.png align="left")

click Update Query.

At the top left corner, rename it from Canada Metrics to Nigeria metrics. Then click **Update Widget**

![](https://cdn-images-1.medium.com/max/1000/1*OBDyNKl2141_Vn2j7gwKpQ.png align="left")

You should now have metrics for both Canada and Nigeria.

Duplicate and edit the pannel for the rest of the Countries.

![](https://cdn-images-1.medium.com/max/1000/1*W7IYUbQJAVnTsCYeVak5gg.png align="left")

#### Dont forget to save your dashboard

> Run the python code again and refresh the dashboard

Re-running the script fetch latest details from OpenWeather. If the weather has changed, you will see the values being updated after refreshing the dashboard.

This looks cool right? I know dude!

You can go further to integrate the script inside AWS Lamda and use AWS EventBridge to trigger the run at interval (let’s say every 20 mins). This will automate the data fetching and if you put the dashboard on auto refresh every 5 minutes, you will be able to see the changes without having to do any other thing.

Phewwww! This is one long article. I hope you learnt something from this long thing?

Confused? Something is not working? Have question(s)? Comment down below and I will be there to untangle you. Lol.

Don’t forget to follow also.