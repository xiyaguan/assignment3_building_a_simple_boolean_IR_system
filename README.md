# assignment3_building_a_simple_boolean_IR_system
Implement a simple Boolean information retreval system supporting "AND" queries over terms and apply it to the TREC corpus. Users of your application should be able to: (1) type in a text query and system will show all the relevant documents; (2) click the article title to view the whole document information (title, full content, etc.).

## Basic Information
- Title: Building a Boolean information retrieval system
- Author: Xiya Guan
- Date: March 8th, 2022
- Description: implement a Boolean information retrieval system that support conjunctive queries over terms.

## Dependency
- Flask==2.0.2
- Jinja2==3.0.3
- pymongo
- nltk==3.5

## Directory Structure
This is our current application structure looks:
```
pa3/
├── __init__.py
├── customized_text_processing.py
├── hw3.py
├── inverted_index.py
├── mongo_db.py
├── pa3_data
│   ├── test_corpus.jl
│   └── wapo_pa3.jl
├── requirements.txt
├── stopwords.txt
├── templates
│   ├── doc.html
│   ├── home.html
│   └── results.html
├── text_processing.py
└── utils.py


```
## What's new in implementation?
In the file utils.py, besides two provided functions (timer and load_wapo), a new function, cleanhtml, is implemented to help to remove HTML tags from text. \
The file customized_text_processing.py contains customized approaches to normalize the text to build inverted index:
- created a more thorough stopword list.
- removed contraction words such as "n't", "'re", "'d",  "'ll", "'s", "'m"...
- cleaned up punctuations but took care of two special cases 1) the colon (:) between digits representing time 2) the period (.) between digits representing decimal numbers. In the two scenarios, the punctuations will be kept.
- Rather than the given text processor, all the numbers are ignored, in the customized approch, the numbers are kelp. Furthermore, the numbers present in word form in an input passage are converted to the traditional numeric (digits) form. For example, "a distance of one hundred and ten meters" will be converted into "a distance of 110 meters".


The logic in this web app is:
- we will first enter query text in the search box in the browser
- and then we will navigate to the web page shows the matched documents
- each page contains 2 results, you can use the next botton to turn to next page
- in the result page, the retreved document is displayed as a short snippet and a title with a link to its full content.


## Run Instructions
First, in the terminal, first run 
```bash
python hw3.py --build --{use_text_processor,use_customized_text_processor}
```
to create the inverted index
Then run
```bash
python hw3.py --run --{use_text_processor,use_customized_text_processor}
```
open up a new browser tab and head to the following URL:
http://127.0.0.1:5000/
You will see the search page where you can type a query and search!\
Note: use the second argument (--{use_text_processor,use_customized_text_processor}) to determine the text processing methods you would like to apply on documents.

### What is the input and output produced?
In our system, a text query is treated as an input; The output are the matched documents.
### How to use the user interface?
Simply enter your text query in the search box.
### The legal input?
Since our corpus is in English, the legal input should be English terms. Note: this is a very simple web app, to find the matched documents, "query_inverted_index" function is implemented to support conjucntive queries over terms and outputs the doc ids, unknown words and ignored words.
### Boundry conditions?
- empty search: No matched documents.
- multiple empty spaces: No matched documents.
- terms in other languages: No matched documents.
- numerical terms or punctuations: Results depends on the titles and content of docs
- more then two spaces between two terms: Converted into only one space and then do the matching between the query and the document's title.

## Testing on "test_corpus"
1. test whether the customized text processor can retrieve all the docs for a query contains a numeric term such as "3 kills".
The picture below shows the matched docs using given text processor
![Screen Shot 2022-03-08 at 9 59 05 PM](https://user-images.githubusercontent.com/79282489/157364219-441e67aa-cf1e-45ee-a569-a59158ad1c4a.png)

The picture below shows the matched docs using customized text processor
![Screen Shot 2022-03-08 at 9 55 10 PM](https://user-images.githubusercontent.com/79282489/157363766-6f022fa6-dbbe-431c-8b48-1f588235000a.png)

You can see we have two retrieved docs when using given text processor, while only one doc using customized text processor. The reason behind this is by using the given text proceesor, numerical tokens are ignored so the website shows all the docs containing "kills"

2. test a special term with collon between two numerical tokens, such as "1:56"
The picture below shows the matched docs using given text processor
![Screen Shot 2022-03-08 at 10 05 39 PM](https://user-images.githubusercontent.com/79282489/157364995-b5657f5a-205b-4b9d-9a50-4e71ee46c8ac.png)


The picture below shows the matched docs using customized text processor
![Screen Shot 2022-03-08 at 10 07 16 PM](https://user-images.githubusercontent.com/79282489/157365087-a2ccf558-2b15-4b86-8830-8bcc46fd82d1.png)
![Screen Shot 2022-03-08 at 10 08 33 PM](https://user-images.githubusercontent.com/79282489/157365232-0b99016a-f9a8-4adc-a1f9-b09e9a9dd8ac.png)

You can notice one matched doc is shown when we using customized text processor and the colon between two number "1" and "56" are kept because we don't want "1:56" converted to "156" once we simply remove all the punctuations.

3. testing on stopwords "Philadelphia losses in a campaign"

The picture below shows the matched docs using customized text processor
![Screen Shot 2022-03-08 at 10 13 55 PM](https://user-images.githubusercontent.com/79282489/157365805-b78a4230-a371-43b7-b8b1-7202998d11c9.png)

Both "in" and "a" are ignored since they are stopwords.

4. Input: "" or " " etc. (multiple empty spaces) \
![Screen Shot 2022-02-10 at 4 55 43 PM](https://user-images.githubusercontent.com/79282489/153503197-2c65a87a-18d1-4fdb-b278-faa809dab4cd.png)


