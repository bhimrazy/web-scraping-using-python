# Web Scraping with Python

Objective : To scrape List of American films of 2022


```python
from urllib.request import urlopen
from urllib.error import HTTPError
from urllib.error import URLError
from bs4 import BeautifulSoup

URL  = "https://en.wikipedia.org/wiki/List_of_American_films_of_2022"


def crawl(url):
  try:
      html = urlopen(URL)
      soup = BeautifulSoup(html.read(), 'html.parser')
      return soup
  except HTTPError as e:
    print(e)
  except URLError as e:
    print('The server could not be found!')
  else:
    print('It Worked!')

soup = crawl(URL)
```


```python
tables = soup.find_all('table',{"class":"wikitable"})
```


```python
# Extracting Highest-grossing films of 2022
hg_table = tables[0]
rows = hg_table.find_all('tr')
header = [h.text.strip() for h in rows[0].find_all('th')] 
highest_grosser_datas=[]

for r in rows[1:]:
  data = {}
  rank = r.find('th').text.strip()
  data[header[0]] = rank
  for i,c in enumerate(r.find_all("td")):
    data[header[i+1]] = c.text.strip()
  print(data)
  highest_grosser_datas.append(data)
```

    {'Rank': '1', 'Title': 'Top Gun: Maverick*', 'Distributor': 'Paramount', 'Domestic Gross': '$716,351,712'}
    {'Rank': '2', 'Title': 'Doctor Strange in the Multiverse of Madness', 'Distributor': 'Disney', 'Domestic Gross': '$411,331,607'}
    {'Rank': '3', 'Title': 'Jurassic World Dominion', 'Distributor': 'Universal', 'Domestic Gross': '$376,009,080'}
    {'Rank': '4', 'Title': 'The Batman', 'Distributor': 'Warner Bros.', 'Domestic Gross': '$369,345,583'}
    {'Rank': '5', 'Title': 'Minions: The Rise of Gru*', 'Distributor': 'Universal', 'Domestic Gross': '$368,807,705'}
    {'Rank': '6', 'Title': 'Thor: Love and Thunder', 'Distributor': 'Disney', 'Domestic Gross': '$343,256,830'}
    {'Rank': '7', 'Title': 'Sonic the Hedgehog 2', 'Distributor': 'Paramount', 'Domestic Gross': '$190,872,904'}
    {'Rank': '8', 'Title': 'Elvis', 'Distributor': 'Warner Bros.', 'Domestic Gross': '$151,040,048'}
    {'Rank': '9', 'Title': 'Uncharted', 'Distributor': 'Sony', 'Domestic Gross': '$148,648,820'}
    {'Rank': '10', 'Title': 'Nope', 'Distributor': 'Universal', 'Domestic Gross': '$123,277,080'}



```python
# Extracting List of films of 2022
lof_datas = []
t1 = tables[1:][0]
for table in tables[1:]:
  rows = table.find_all("tr")
  header = [h.text.strip() for h in rows[0].find_all('th')][:-1]
  for r in rows[1:]:
    data = {}
    cols = r.find_all('td')[-4:-1]
    for i,c in enumerate(cols):
      data[header[i+1]] = c.text.strip()
    lof_datas.append(data)
```


```python
# let's write these to csv file
from typing import *
def write_to_csv(datas:List[Dict],file_name:str):
  """This function writes all the data present in datas dict to a csv file.

  Parameters:
  -----------
  datas: List[Dict] -> List of dict 
  file_name:str -> csv file name
  """
  keys = datas[0].keys()
  content = ""
  content += "|".join(keys) + "\n"
  for d in datas:
    content += "|".join(d.values()) + "\n"
  filename = file_name + ".txt"
  with open(filename,"w") as f:
    f.write(content)

write_to_csv(highest_grosser_datas,"highest_grosser")
write_to_csv(lof_datas,"list_of_films")

```


```python
df = pd.read_csv("list_of_films.txt",sep="|")
df.head()
```





  <div id="df-df9dcfc7-3e0f-4b1f-b5e5-2e4d54ed166e">
    <div class="colab-df-container">
      <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Title</th>
      <th>Production company</th>
      <th>Cast and crew</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>The 355</td>
      <td>Universal Pictures / Freckle Films / FilmNatio...</td>
      <td>Simon Kinberg (director/screenplay); Theresa R...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>The Legend of La Llorona</td>
      <td>Saban Films</td>
      <td>Patricia Harris Seeley (director/screenplay); ...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>The Commando</td>
      <td>Saban Films</td>
      <td>Asif Akbar (director); Koji Steven Sakai (scre...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>American Siege</td>
      <td>Vertical Entertainment</td>
      <td>Edward Drake (director/screenplay); Timothy V....</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Scream</td>
      <td>Paramount Pictures / Spyglass Media Group</td>
      <td>Matt Bettinelli-Olpin, Tyler Gillett (director...</td>
    </tr>
  </tbody>
</table>
</div>
      <button class="colab-df-convert" onclick="convertToInteractive('df-df9dcfc7-3e0f-4b1f-b5e5-2e4d54ed166e')"
              title="Convert this dataframe to an interactive table."
              style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M0 0h24v24H0V0z" fill="none"/>
    <path d="M18.56 5.44l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94zm-11 1L8.5 8.5l.94-2.06 2.06-.94-2.06-.94L8.5 2.5l-.94 2.06-2.06.94zm10 10l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94z"/><path d="M17.41 7.96l-1.37-1.37c-.4-.4-.92-.59-1.43-.59-.52 0-1.04.2-1.43.59L10.3 9.45l-7.72 7.72c-.78.78-.78 2.05 0 2.83L4 21.41c.39.39.9.59 1.41.59.51 0 1.02-.2 1.41-.59l7.78-7.78 2.81-2.81c.8-.78.8-2.07 0-2.86zM5.41 20L4 18.59l7.72-7.72 1.47 1.35L5.41 20z"/>
  </svg>
      </button>

  <style>
    .colab-df-container {
      display:flex;
      flex-wrap:wrap;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

      <script>
        const buttonEl =
          document.querySelector('#df-df9dcfc7-3e0f-4b1f-b5e5-2e4d54ed166e button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-df9dcfc7-3e0f-4b1f-b5e5-2e4d54ed166e');
          const dataTable =
            await google.colab.kernel.invokeFunction('convertToInteractive',
                                                     [key], {});
          if (!dataTable) return;

          const docLinkHtml = 'Like what you see? Visit the ' +
            '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
            + ' to learn more about interactive tables.';
          element.innerHTML = '';
          dataTable['output_type'] = 'display_data';
          await google.colab.output.renderOutput(dataTable, element);
          const docLink = document.createElement('div');
          docLink.innerHTML = docLinkHtml;
          element.appendChild(docLink);
        }
      </script>
    </div>
  </div>





```python
import pandas as pd
import matplotlib.pyplot as plt

df = pd.read_csv("highest_grosser.txt",sep="|")
df["gross_amt"] = df["Domestic Gross"].apply(lambda x : int(x.replace("$","").replace(",","")))
df
```





  <div id="df-139e9deb-a872-4d9b-ba6a-221445d52f94">
    <div class="colab-df-container">
      <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Rank</th>
      <th>Title</th>
      <th>Distributor</th>
      <th>Domestic Gross</th>
      <th>gross_amt</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Top Gun: Maverick*</td>
      <td>Paramount</td>
      <td>$716,351,712</td>
      <td>716351712</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>Doctor Strange in the Multiverse of Madness</td>
      <td>Disney</td>
      <td>$411,331,607</td>
      <td>411331607</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Jurassic World Dominion</td>
      <td>Universal</td>
      <td>$376,009,080</td>
      <td>376009080</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>The Batman</td>
      <td>Warner Bros.</td>
      <td>$369,345,583</td>
      <td>369345583</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>Minions: The Rise of Gru*</td>
      <td>Universal</td>
      <td>$368,807,705</td>
      <td>368807705</td>
    </tr>
    <tr>
      <th>5</th>
      <td>6</td>
      <td>Thor: Love and Thunder</td>
      <td>Disney</td>
      <td>$343,256,830</td>
      <td>343256830</td>
    </tr>
    <tr>
      <th>6</th>
      <td>7</td>
      <td>Sonic the Hedgehog 2</td>
      <td>Paramount</td>
      <td>$190,872,904</td>
      <td>190872904</td>
    </tr>
    <tr>
      <th>7</th>
      <td>8</td>
      <td>Elvis</td>
      <td>Warner Bros.</td>
      <td>$151,040,048</td>
      <td>151040048</td>
    </tr>
    <tr>
      <th>8</th>
      <td>9</td>
      <td>Uncharted</td>
      <td>Sony</td>
      <td>$148,648,820</td>
      <td>148648820</td>
    </tr>
    <tr>
      <th>9</th>
      <td>10</td>
      <td>Nope</td>
      <td>Universal</td>
      <td>$123,277,080</td>
      <td>123277080</td>
    </tr>
  </tbody>
</table>
</div>
      <button class="colab-df-convert" onclick="convertToInteractive('df-139e9deb-a872-4d9b-ba6a-221445d52f94')"
              title="Convert this dataframe to an interactive table."
              style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M0 0h24v24H0V0z" fill="none"/>
    <path d="M18.56 5.44l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94zm-11 1L8.5 8.5l.94-2.06 2.06-.94-2.06-.94L8.5 2.5l-.94 2.06-2.06.94zm10 10l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94z"/><path d="M17.41 7.96l-1.37-1.37c-.4-.4-.92-.59-1.43-.59-.52 0-1.04.2-1.43.59L10.3 9.45l-7.72 7.72c-.78.78-.78 2.05 0 2.83L4 21.41c.39.39.9.59 1.41.59.51 0 1.02-.2 1.41-.59l7.78-7.78 2.81-2.81c.8-.78.8-2.07 0-2.86zM5.41 20L4 18.59l7.72-7.72 1.47 1.35L5.41 20z"/>
  </svg>
      </button>

  <style>
    .colab-df-container {
      display:flex;
      flex-wrap:wrap;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

      <script>
        const buttonEl =
          document.querySelector('#df-139e9deb-a872-4d9b-ba6a-221445d52f94 button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-139e9deb-a872-4d9b-ba6a-221445d52f94');
          const dataTable =
            await google.colab.kernel.invokeFunction('convertToInteractive',
                                                     [key], {});
          if (!dataTable) return;

          const docLinkHtml = 'Like what you see? Visit the ' +
            '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
            + ' to learn more about interactive tables.';
          element.innerHTML = '';
          dataTable['output_type'] = 'display_data';
          await google.colab.output.renderOutput(dataTable, element);
          const docLink = document.createElement('div');
          docLink.innerHTML = docLinkHtml;
          element.appendChild(docLink);
        }
      </script>
    </div>
  </div>





```python
x = df["Title"]
y = df["gross_amt"]

plt.figure(figsize=(16,8),dpi=100)
plt.title("Highest-grossing films of 2022")
plt.xlabel("Movies")
plt.ylabel("Gross Amount in 100M $")
plt.xticks(rotation=90)
plt.bar(x,y)
for i, (amt,a) in enumerate(zip(df["Domestic Gross"],y)):
    plt.text(i, a, f' {amt} ',
             ha='center', va='bottom', color='black',fontsize = 10)
plt.xlim(-0.6, len(x)-0.4)
plt.show()
```


![png](Web_Scraping_with_Python_files/Web_Scraping_with_Python_8_0.png)



```python
jupyter nbconvert --to FORMAT notebook.ipynb
```
