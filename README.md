# README: Web Scraping Electoral Data Using Selenium

## Introduction

This project is focused on web scraping electoral data from the [Infogob JNE](https://infogob.jne.gob.pe/Eleccion) website using Python and Selenium. The script automates the process of navigating the website, selecting electoral processes, extracting relevant data, and saving it into an Excel file.
Ensure that you have a stable internet connection while running the script to avoid timeouts and errors in web scraping.

## Prerequisites

Make sure you have the following installed before running the script:

1. **Python 3.8 or higher**
2. **Google Chrome** installed on your system.
3. **Chromedriver** matching your version of Chrome. You can download it from [ChromeDriver](https://sites.google.com/a/chromium.org/chromedriver/downloads).
4. **Selenium** Python package.

### Installing Required Libraries

Install Selenium and related libraries using pip:

```bash
!pip install selenium
```

The following libraries are required for the script:

```python
from selenium import webdriver
from selenium.webdriver.chrome.service import Service
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import Select
from selenium.webdriver.common.action_chains import ActionChains
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
import pandas as pd
import time
import re
```

## Setup Instructions

### Step 1: Setting Up the Driver

1. Install and configure **ChromeDriver** using the following code:

```python
from webdriver_manager.chrome import ChromeDriverManager
from selenium import webdriver
from selenium.webdriver.chrome.service import Service

service = Service(executable_path="chromedriver-win64/chromedriver")
options = webdriver.ChromeOptions()
driver = webdriver.Chrome(service=service, options=options)
```

Make sure to download the correct **Chromedriver** version and set the `executable_path` to its location on your system.

### Step 2: Navigating the Website

Once the driver is set up, the next step is to access the electoral page:

```python
url = 'https://infogob.jne.gob.pe/Eleccion'
driver.maximize_window()
driver.get(url)
time.sleep(5)
```

### Step 3: Selecting the Process Type

Use Selenium to select the desired process type, such as **Presidential Elections**, **Congressional Elections**, etc.

```python
# Example for selecting Presidential Elections
tipo_proceso_xpath = '/html/body/div[1]/section/div[2]/div[2]/div[2]/div[1]'
tipo_xpath = '//*[@id="section"]/div[2]/div[2]/div[2]/div[1]/div/div[2]/div[2]'
driver.find_element(By.XPATH, tipo_proceso_xpath).click()
time.sleep(2)
driver.find_element(By.XPATH, tipo_xpath).click()
```

### Step 4: Extracting the Data

The script loops through the available elections, selects them, and extracts the relevant data from the results tables. This data is stored in a Pandas DataFrame.

```python
data = pd.DataFrame(columns=['Elecciones', 'ORGANIZACIÓN POLÍTICA', 'TOTAL VOTOS'])

for i in range(2, len(procesos) + 1):
    # Re-select the election type and navigate
    driver.get(url)
    driver.find_element(By.XPATH, tipo_proceso_xpath).click()
    driver.find_element(By.XPATH, tipo_xpath).click()

    # Extract election data
    eleccion_xpath = f'/html/body/div[1]/section/div[2]/div[2]/div[2]/div[2]/div/div[2]/div[{i}]'
    electoral = driver.find_element(By.XPATH, eleccion_xpath).text
    driver.find_element(By.XPATH, eleccion_xpath).click()

    # Extract table data
    tabla_resultado_xpath = '//*[@id="CandidatosResultados"]/div/div[1]/div[2]/div[2]'
    tabla_resultado = driver.find_element(By.XPATH, tabla_resultado_xpath)
    tabla_electoral = pd.read_html(tabla_resultado.get_attribute('innerHTML'))[0]
    tabla_electoral['Elecciones'] = electoral

    data = pd.concat([data, tabla_electoral[['Elecciones', 'ORGANIZACIÓN POLÍTICA', 'TOTAL VOTOS']]], ignore_index=True)
```

### Step 5: Saving Data to Excel

Once the data extraction is complete, you can save it to an Excel file using Pandas.

```python
data.to_excel('Elecciones_presidenciales_group4_ass_5_2024.xlsx', index=True)
```

### Step 6: Closing the Driver

Make sure to close the browser instance after the script finishes running:

```python
driver.quit()
```

