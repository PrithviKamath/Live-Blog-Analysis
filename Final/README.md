
<img src="https://github.com/PrithviKamath/Live-Blog-Analysis/blob/master/Final/Extra/WebHose.io.png"></img> <br />

<b>About WebHose</b> <br />
WebHose provides on-demand access to web data feeds anyone can consume. Webhose.io empowers its customers to build, launch, and scale data-driven operations as they grow -- whether they are an entrepreneur, a researcher, or a senior executive at a Fortune 500 company. Developers get free access to the same web data feeds that power their growing customer base of global media analytics and monitoring leaders. Every web data feed is optimized to deliver up-to-the-minute coverage of a specific content domain, such as news, blogs, online discussions, and more. Just define your filters so you can focus on what you do best.  <br />
Webhose.io is the brainchild of Ran Geva and Guy Mor, two entrepreneurs with extensive experience in technology, data mining, and product development who set up to build a simple solution for a complicated problem for anyone who wants to consume data from the web. <br /> <br />

<b>Dataset Summary:</b> <br />

Each JSON files has the below structure <br /> <br />

<img src="https://github.com/PrithviKamath/Live-Blog-Analysis/blob/master/Final/Extra/Dataset%20Summary.PNG"></img> <br />  <br />

I have followed the below steps to  <br />
• Download live data <br />
• Store the raw data <br />
• Perform Data Wrangling and generate processed data <br />
• Store Processed Data <br />
• Use Processed Data to perform analysis <br /> <br />

<b>Download Live Data</b> <br />
<b>Approach:</b> <br />
• Sign In to WebHose.io to get a free token (API Key) <br />
• Set this token as an environmental variable for easy accessibility <br />
• Pass ‘Reviews’ as a query to get 100 latest blogs, news and discussion about various reviews on the internet in JSON format. (Each JSON file represents one blog) <br /> <br />

<img src="https://github.com/PrithviKamath/Live-Blog-Analysis/blob/master/Final/Extra/Download%20Data.PNG"></img> <br /> <br />

<b>Store Raw Data</b> <br />
<b>Approach:</b> <br />
• Get the working directory and move to its parent directory to start creating a folder structure <br />
• A function checks whether a given folder exits. If not, it creates a folder with the given name and returns the new path, else if the folder already exists, it updates and returns the new path <br />
• Use the above function to check for a folder named ‘Final’ <br />
• Perform the same operation to check for ‘Data’ and ‘Input Data’ folders <br />
• Dynamically create folders for each country the JSON files belongs to and save those JSON files in their respective country folders <br />
• If the country folders already exist, add the new JSON files in them <br />
• The naming format for each JSON file is: ‘UUID_time_stamp’ for easy identification <br />
• JSON files with missing country tag are moved to ‘Not Mentioned’ folder <br /> <br />

<img src="https://github.com/PrithviKamath/Live-Blog-Analysis/blob/master/Final/Extra/Data%20Storage.PNG"></img> <br /> <br />

<b>File</b>: API_download_store.ipymb <br /> <br />

<b>Perform Data Wrangling and generate processed data</b> <br />
<b>Data Concatenation:</b> <br />
<b>Approach:</b> <br />
• Read all the JSON files from ‘/Final/Data/Input Data’ into a DataFrame <br />
• As we know that these are nested JSON files, merge the keys from inner JSON with the keys from the outer JSON using lambda function <br />
• Drop redundant columns from this DataFrame <br /> <br />

<b>Handling Missing Data</b> <br />
• Check if the dataset has missing values and get a count and percentage of missing values for each attribute <br />
• From this we understand that ‘rating’ has 99% missing values. We therefore ignore this variable in our analysis. <br />
• ‘domain_rank’ has 55% missing values. To fill these missing values we have to understand what ‘domain_rank’ represents and what are the factors it is based on.  <br />
• ‘main_image’ is the image URL and its missing values are filled with ‘NA’ <br /> <br />

<b>File</b>: Get_processed_data.ipymb <br /> <br />

We shall hence forth be using cleaned and processed file stored in 'Processed Data' folder to perform analysis. <br /> <br />

<b>Analysis 1:</b> Tf-idf (Text frequency- Inverse Document Frequency) <br />
We implement this algorithm to understand what is the current trending topic among blogers. This algorithm calculates the weight for each word based on the its occurrence in the file and compares it with words in all other files. It reduces the weight of words which appears in multiple files thereby increasing the weight of unique words of each file. <br /> <br />

<b>Explanation</b> <br />
• Tf(word,blob): Calculates term frequency for every word normalized by dividing by the total number of words in the file. <br />
• N_containing(word,bloblist): Returns the number of documents containing the word passed in the argument. <br />
• Idf(word,bloblist): Computes Inverse Document Frequency by computing how the occurrence of the word. I take the ratio of total number of documents to the number of documents in which the word occurs and take a log of this. We also add 1 to the divisor to prevent division by 0. <br />
• Tfidf(word,blob,bloblist): Computes tfidf score by multiplying tf and idf values calculated above. <br />
• We then use NLTK package to remove the stop words in English and generate a ‘tfanalysis’ DataFrame containing only the text  <br /> <br />

<img src="https://github.com/PrithviKamath/Live-Blog-Analysis/blob/master/Final/Extra/tf-idf.PNG"></img>  <br /> <br />

<b>Approach:</b> <br />
• Remove punctuations from the text <br />
• Remove stop words using NLTK package <br />
• Convert the entire text to BlobText object <br />
• Find the score for each word using the above defined functions <br />
• Display only the highest rated word for each file <br />
 <br />
Tf-idf takes 1 hour to analyze 700 blogs. <br /> <br />
<b>Conclusion:</b> <br />
• On observing the trend, we understand that there is a large contribution of people from US to writing blogs and replying to blogs <br />
• As this trend is continuous, we can also conclude that they contribute on an everyday basis <br /> <br />

<b>Input File</b>: Analysis1.ipymb <br />
<b>Output File</b>: Analysis1.csv in 'Final/Data/Analysis/Analysis 1' folder<br /> <br />

<img src="https://github.com/PrithviKamath/Live-Blog-Analysis/blob/master/Final/Analysis/Analysis%201/Analysis%201.PNG"></img>  <br /> <br />

<b>Analysis 2:</b> Get country wise distribution of number of people contributing to blog writing and replying to various blog over the past month. <br /> <br />

<b>Approach:</b> <br />
• Convert the published_date to datetime format <br />
• Extract day and save it into a new derived variable named ‘published_day’ <br />
• Get top 20 countries which contribute to blog writing using groupby function on ‘published_day’ and ‘country’ variable <br />
• Get top 20 countries which contribute to replying to blog using groupby function on ‘published_day’ and ‘country’ and getting a count of ‘replies_count’ <br />
• Use matplotlib to generate subplots <br />
• Generate graphs usgin seaborn for the above generated DataFrames <br /> <br />
<b>Conclusion:</b> <br />
• On observing the trend, we understand that there is a large contribution of people from US to writing blogs and replying to blogs <br />
• As this trend is continuous, we can also conclude that they contribute on an everyday basis <br /> <br />

<b>File</b>: Analysis2.ipymb <br /> <br />

<img src="https://github.com/PrithviKamath/Live-Blog-Analysis/blob/master/Final/Analysis/Analysis%202/Analysis%202.PNG"></img>    <br /> <br />

<b>Analysis 3:</b> Find the most preferred language to write a blog <br /> <br />

<b>Approach:</b> <br />
• Group the entire dataset based on languages that blogs are published in <br />
• Get the various ‘site_type’ supported by various languages <br />
• Get the count of replies for blogs in each language <br /> <br />

<b>Conclusion:</b> <br />
English is the most preferred language to write a blog because of the following: <br />
• It is the only language that supports all 3 types of articles that is blog, news and discussions <br />
• It also has the highest number of replies compared to other languages. <br /> <br />

<b>File</b>: Analysis3.ipymb <br /> <br />

<img src="https://github.com/PrithviKamath/Live-Blog-Analysis/blob/master/Final/Analysis/Analysis%203/Analysis%203_1.PNG"></img>
<img src="https://github.com/PrithviKamath/Live-Blog-Analysis/blob/master/Final/Analysis/Analysis%203/Analysis%203_2.PNG"></img>
<img src="https://github.com/PrithviKamath/Live-Blog-Analysis/blob/master/Final/Analysis/Analysis%203/Analysis%203_3.PNG"></img>

 <br /> <br />

I have presented my analysis on 702 blogs that I have downloaded using the API. The analysis are bound to change slightly if new data is downloaded using 'API_download_store.ipymb' file. To get the exact analysis please run the analysis on the data that is available in '/Final/Data/Input Data/' before downloading new data.  <br />  <br />

<b>Instruction to run the project:</b> <br /> <br />
• Open GitBash in the location you have saved the above downloaded repository  <br />
• Export the token key as shown in the below screen shot <br /> <br />
<img src="https://github.com/PrithviKamath/Live-Blog-Analysis/blob/master/Final/Extra/API_key_instruction.PNG"></img> <br /><br />
• Open jupyter notebook from this GitBash and run the 'API_download_store.ipymb' file <br /> <br />
<img src="https://github.com/PrithviKamath/Live-Blog-Analysis/blob/master/Final/Extra/Jupyter_notebook.PNG"></img> <br /><br />
• Run the 'Get_processed_data.ipymb' file to get processed data from raw files <br />
• Run the 'Analysis1.ipymb' file to get output for first analysis. Output will be saved in '/Final/Analysis/Analysis 1' <br />
• Run the 'Analysis2.ipymb' file to get output for second analysis. Output is a seaborn graph that is display in jupyter notebook <br />
• Run the 'Analysis3.ipymb' file to get output for third analysis. DataFrames are shown as output in jupyter notebook <br />
