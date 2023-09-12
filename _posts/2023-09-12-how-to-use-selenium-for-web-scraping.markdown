---
layout: post
title: How to use selenium for web scraping
categories: misc
tags: [misc]
---

At times, we may need to extract data from internet that is dynamically rendered by JavaScript.
Selenium comes in handy.

# Install chromedriver and chrome
https://sites.google.com/chromium.org/driver/downloads
Note: the version of chromedriver and chrome should be same.

# Install selenium
```Bash
pip install selenium
```

# Open new chrome
```Python
from selenium import webdriver
from selenium.webdriver.chrome.service import Service
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.common.exceptions import TimeoutException

# Set the path to the ChromeDriver executable
chromedriver_path = '/usr/local/bin/chromedriver'
s = Service(chromedriver_path)

# Create a Chrome WebDriver instance
driver = webdriver.Chrome(service=s)

# Navigate to a website
driver.get('https://www.tradingview.com/')

# wait up to 10 seconds for page to load
timeout = 10
try:
    WebDriverWait(driver, timeout).until(EC.visibility_of_element_located((By.XPATH, "/html/body/div[1]")))
except TimeoutException:
    print("Timed out waiting for page to load")
    driver.quit()

usermenu = driver.find_element(By.XPATH, "//button[@aria-label='Open user menu']").click()

try:
    WebDriverWait(driver, timeout).until(EC.visibility_of_element_located((By.XPATH, "//button[@data-name='header-user-menu-sign-in']")))
except TimeoutException:
    print("Timed out waiting for page to load")
    driver.quit()
```
As shown in code above, it 
1. Open browser and direct to 'https://www.tradingview.com'
2. Find a button with attribute aria-label = 'Open user menu' and click it.

# Connect to exist chrome
Sometimes we need to keep session, connecting to exist chrome is a better choice.

Open chrome in debug mode  
```Bash
/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome --remote-debugging-port=9222
```

Connect to chrome  
```Python
chromedriver_path = '/usr/local/bin/chromedriver'

# Create a Chrome WebDriver instance
s = Service(chromedriver_path)
option = webdriver.ChromeOptions()
option.debugger_address = "127.0.0.1:9222"

# Create a Chrome WebDriver instance
driver = webdriver.Chrome(service=s, options=option)
```

# How to get XPATH of html element
1. Find the element in UI
2. Right click and choose the last item
3. Right click on the code area and choose copy->copy XPath

