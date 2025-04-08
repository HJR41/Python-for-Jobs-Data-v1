# Reed Job Board API Scrapper - Python
## Introduction
This script was developed to extract job data from the Reed API in order to collect open job positions and support myself and my colleagues during the restructuring at our previous employer. Take a look at the script [**here.**](reed_scrapper/2_Project/reed_jobs_api_scrapper_v1.0.ipynb)

Data is transformed within the script and joined to locational data using the pgeocode API then exported to a csv for further analysis.

## Tools Used

The project was built using Python, with a mixture of standard libraries and third-party packages. Inluding:

- **python-dotenv** - Used to securely manage API keys through environment variables.
- **requests** - Enables seamless API integration such as the .get() method for fetching data.
- **pgeocode** - Used to reference the Python Standard API for retrieving job location data based on postcodes.
- **pandas** - The Data Manipulation powerhouse. In this project, used to facilitate renaming and reordering columns, handling datetime objects and location data enrichment with dataset joints.


## Method

The parameters for the search are first defined and then appended to the API URL. Using the requests library, the API is called, and if the request is successful, the JSON response is retrieved — up to a maximum of 10,000 jobs (API limit). The JSON data is then parsed and loaded into a DataFrame.
```python
response = requests.get(url, auth=(reed_api_key, ''))
json_for_df = [] # initialise list to extend reed_data to.
if response.status_code == 200:
    print(f"Response Success {response.status_code}") 
    reed_data = response.json() # get json and set it to reed data. Add reed data to json_for_df below
    json_for_df.extend(reed_data["results"])
    resultsToSkip = resultsToSkip + resultsToTake # increment results to skip so i don't append the same data in the loop below
    total_results = (reed_data["totalResults"])
else:
    raise Exception (f"Unsuccesfull API request: {response.status_code}") # stops execution of code and exits with error message.

```



Once in the DataFrame:

- Date and time processing is applied to relevant columns.

- Unnecessary columns are removed.

- Key columns are renamed and reordered for clarity.

- The DataFrame is then split into two parts:

- Jobs with postcode-based location data.

- Jobs with town name–based location data.

Both subsets are then joined with the pgeocode DataFrame to enrich the data with more detailed geographic information. Finally, the separate DataFrames are concatenated into a single DataFrame, ready for export to CSV.


## Notes and Limitations

This section outlines known constraints and considerations when using the script and its data sources.

- The Reed API has a limit of 10,000 jobs per request.
- **pgeocode** works best with UK postcodes — accuracy may vary with town names.
- Data quality may depend on how complete the Reed job postings are.

## Future Improvements

This section outlines potential features or enhancements that could be implemented in future versions of the script:

- Add logging to monitor script progress and API call status.

- Implement retry logic for failed API calls to ensure robustness.

- Add filters to target specific industries or job titles for more refined searches.

- Automate regular scraping via a scheduler (e.g., cron or Airflow) for ongoing data collection.

This script worked for automating job searched from one Job Board. A future iteration of the project should compile Job Postings from multiple sorces. #

## Conclusions

This script provides a practical solution for extracting and processing job data from the Reed API. By integrating location data and transforming the information into a structured format, it enables efficient analysis and decision-making. With further enhancements, this tool could become even more powerful and adaptable for different use cases.


