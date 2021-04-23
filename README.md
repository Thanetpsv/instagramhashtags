# instagramhashtags
Web scraping for related instagram hastags and automatically creates a CSV file.

#!/usr/bin/env python
# coding: utf-8

# In[1]:


from selenium import webdriver
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from bs4 import BeautifulSoup


# In[1]:


base_url = 'https://www.instagram.com/explore/tags/'
components = str(input("Enter desired #: "))

def make_url(base, comp):
    url = base_url
    for r in comp:
        url = "{}{}".format(url, r)
    return url
desired_url = make_url(base_url, components)
print(desired_url)


# In[3]:


PATH = "C:\Program Files (x86)\chromedriver.exe"
driver = webdriver.Chrome(PATH)
driver.get(desired_url)


# In[4]:


post = driver.find_element_by_xpath("/html/body/div[1]/section/main/header/div[2]/div[1]/div[2]/span/span").text

try:
    related = driver.find_element_by_xpath("/html/body/div[1]/section/main/header/div[2]/div[2]/span/span[2]")
    related_list = related.text.split("#")[1:]
    related_list = [components] + related_list
    driver.quit()
except:
    print("No related hashtags")
    print("{} posts {}".format(post, components))
    driver.quit()
    quit()


# In[5]:


from datetime import datetime
from datetime import date

now = datetime.now()

current_time = now.strftime("%H:%M:%S")
today = date.today()


# In[6]:


url_list = []
for hasht in related_list:
    url_list.append(make_url(base_url, hasht))

post_list = []
for sub_url in url_list:
    PATH = "C:\Program Files (x86)\chromedriver.exe"
    driver = webdriver.Chrome(PATH)
    driver.get(sub_url)
    post_list.append(driver.find_element_by_xpath("/html/body/div[1]/section/main/header/div[2]/div[1]/div[2]/span/span").text)
    driver.quit()


# In[7]:


post_list_int = []
for num in post_list:
    post_list_int.append(int(num.replace(',', '')))

import pandas as pd
concat = {'Hashtags':related_list,'Number of posts':post_list_int}
df = pd.DataFrame(concat)
df['date(y-m-d)'] = today
df['time(H-M-S)'] = current_time
df = df.sort_values(by='Number of posts', ascending=False)


# In[9]:


today_list = str(today).replace('-', " ").split()
current_time_list = str(current_time).replace(":", " ").split()

h = current_time_list[0]
mi = current_time_list[1]
s = current_time_list[2]

y = today_list[0]
m = today_list[1]
d = today_list[2]

filename = ("{} d{} m{} y{} h{} mi{}".format(components, d , m, y, h, mi))
df.to_csv('D:\Coding\FLC project\IG_trend_csv\{}.csv'.format(filename), index = False)
df


# In[ ]:




