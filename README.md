# MoneyHoney
For this project, imagine you are working for a leading quantitative investment fund, MoneyHoney, that is looking to leverage LLMs to gain a competitive edge in stock selection. The company wants to build a system that can analyze vast amounts of textual data to identify promising investment opportunities before they become obvious to the market.

Research Automation: Build a system that can find relevant stocks based on natural language queries from the user (e.g. "What are companies that build data centers?"). All stocks on the New York Stock Exchange must also be searchable by metrics such as Market Capitalization, Volume, Sector, and more.

## Table of Contents
- [Tools Used](#tools-used)
- [Development Notes](#development-notes)
- [Development Issues](#development-issues)
- [Resources](#resources)
- [Future](#future)

## Tools Used
- Python
- Pinecone
- Google Colab

## Development Notes
### [concurrent.futures](https://docs.python.org/3/library/concurrent.futures.html)
*Python library that provides high-level interface for asynchronously executing callables*

### Function vs Callable
  ***In Python, the concepts of "function" and "callable" are closely related but not identical. Here's a breakdown of the difference:*** <br>
  
  **Function:**
   - A function is a block of organized, reusable code that is used to perform a single, specific action.
   - Functions are defined using the def keyword.
   - They can take arguments (input values) and return values (output).
   - Functions are a fundamental building block of Python programming.
     
  **Callable:** <br>
   - A callable is any object that can be called or invoked like a function using the parentheses () notation.
   - Functions are a type of callable, but not all callables are functions.
   - Other examples of callables include:
     - *Classes:* When you call a class, it creates a new instance (object) of that class.
     - *Methods:* Methods are functions that are defined within a class.
     - *Lambda functions:* Anonymous functions created using the lambda keyword.
     - Objects with a __call__ method: Any object that defines a __call__ method can be treated as a callable.
       
  ***Key Differences:*** <br>
  - Scope:<br>
    Functions are a specific type of callable, whereas callables encompass a broader category of objects.
  - Definition:<br>
    Functions are defined using the def keyword, while callables can be created through various means (classes, methods, lambda functions, etc.).
  - Purpose:<br>
    Functions are primarily used for encapsulating code logic, while callables offer a more generic way to invoke objects that behave like functions. <br>
  ***Checking for Callability:*** <br>
You can use the built-in callable() function to check if an object is callable.

### Workers per Core
- *Basic rule:* One worker per core. 
- *Hyperthreading:* If your CPU supports hyperthreading, you could potentially have double the number of workers as each core can handle multiple threads simultaneously. 
- *Workload intensity:* For lighter workloads, you may be able to get away with slightly more workers per core, but for heavy processing tasks, sticking closer to a 1:1 ratio is recommended.

### Parallel batch processing:
**Concurrent processing:** <br>
Multiple items from different batches can be processed at the same time across multiple threads or processors. 
**Chunk-based processing:** <br>
Batches are often divided into smaller chunks, and each chunk can be processed independently, enabling parallel execution. 
**No need to wait for full batch completion:** <br>
Once an item within a batch is finished processing, the system can move on to an item from the next batch. 

## Development Issues

#### Adding Vectors To Pinecone
1. While processing the stock and ticker information and storing the data into Pinecone, I ran into ["Input should be valid str"](https://docs.pydantic.dev/2.10/errors/validation_errors/#string_type) error for the PHYS stock (shown in image below). This caused the logs of "Successful *stockSymbol*" and errors to stop being printed onto colab console but the data was still being processed and stored into Pinecone. This happened due to YahooFinance not having data on PHYS stock and the code "Stopping execution" and exit program once an error is received. I updated the code that it would exit from the current executable since there was an error but would continue to log the rest of the batch processes.

![image](https://github.com/user-attachments/assets/18e06725-9fff-4800-9a90-6786104db237)

2. Processing time for storing the initial data for stocks took well over an hour for roughly 6000 stocks. (Stopped execution after URL error)
  
3. Initial run had around 3000 successful vectors stored with around 3000 unsuccessful vectors stored ==> Had to restart and rerun (only unsuccessful and not processed items were processed)
  
4. Received following error message but it didn't affect the remaining processes, advised that as long as 9037 stocks process we can utilize the vectors effectively.

   ERROR:yfinance:404 Client Error: Not Found for url: https://query2.finance.yahoo.com/v10/finance/quoteSummary/FACT?modules=financialData%2CquoteType%2CdefaultKeyStatistics%2CassetProfile%2CsummaryDetail&corsDomain=finance.yahoo.com&formatted=false&symbol=FACT&crumb=IqIBjmVBhRW


## Resources
- [What is Quantitative Analysis?](https://www.investopedia.com/articles/investing/041114/simple-overview-quantitative-analysis.asp)
- [Text Classification with LLMs](https://hussainpoonawala.medium.com/text-classification-with-large-language-models-llms-a23c731a687e)
- [Sentiment Trading with LLMs](https://www.sciencedirect.com/science/article/pii/S1544612324002575)
- [Stock Market Sentiment Prediction with OpenAI](https://www.insightbig.com/post/stock-market-sentiment-prediction-with-openai-and-python)
- [SEC Stock Market Data](https://www.sec.gov/data-research/sec-markets-data)
- [Serving an LLM Application as an API Endpoint](https://www.datacamp.com/tutorial/serving-an-llm-application-as-an-api-endpoint-using-fastapi-in-python)
- [Nasdaq Screener](https://www.nasdaq.com/market-activity/stocks/screener)
- [Accelerating LLM Operations with parallel-parot](https://bradito.me/blog/parallel-parrot/)
- [Yahoo Finance API in Python](https://www.geeksforgeeks.org/get-financial-data-from-yahoo-finance-with-python/)
- [Free Worldwide News API](https://www.thenewsapi.com/)
- [Understanding Metadata - Pinecone](https://docs.pinecone.io/guides/data/understanding-metadata)
- [PineconeVectorStore](https://api.python.langchain.com/en/latest/pinecone/vectorstores/langchain_pinecone.vectorstores.PineconeVectorStore.html)
- [Metadata Query Language](https://docs.pinecone.io/guides/data/understanding-metadata#metadata-query-language)

# Future
1. Market Firehose: Build a system that can handle 100 articles per minute. Your system should be able to process unstructured text articles and parse out the publisher, author, date, title, body, related sector. This should include an API and database schema. It must be a highly extensible system that can support articles from many different feeds, allows others to subscribe to the system to receive structured articles, and must operate as close to real time as possible while being able to handle processing hundreds of articles per minute.

2. Market Analysis: Build a system that can process articles received from the market firehose.It should determine which companies are related to the article, a sentiment per company (positive or negative), and an event type classification for each article. Remember, this system must operate as close to real time as possible, and your system may receive hundreds of articles per minute.
