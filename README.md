## Automated Credit Analysis
Our goal is to create a scalable proof-of-concept of automated credit analysis.
- Our CEO asked us for [some analysis](prompt.md).
- Our input data is [this bank statement](statements/idfc_first_bank.pdf).
- [Docsumo.ipynb](docsumo.ipynb) calls Docsumo and saves output to disk.
- [Analysis.ipynb](analysis.ipynb) calls OpenAI, computes some ratios, and outputs this [Credit analysis](credit_analysis.pdf).

## Focus on IDFC borrower
I opted to focus on the Indian borrower because his bank statements told a story. It was eye-opening to see him shuffling loans. 

The techniques used should be extendable to different bank statements easily.


## Approach
To win the trust of the CEO, we'll address most of the points in his requests. 
1. State withdrawals and deposits for each month.
2. Detect transactions with lenders.
3. Comment on large transactions.
4. Raise red flags that should be deal-breakers for lending to the applicant.
5. Write up the findings.

I decided the highest reward-to-effort was to focus on the lender's current debt burden.


## Doc parsing 
I evaluated a half dozen options for parsing the document.

[LLMSherpa](https://github.com/nlmatics/llmsherpa) solves a lot of problems with using LLMs to parse large PDFS, but it fails badly at parsing tables out of docs.

The best free options for parsing tables out of PDFS are Camelot and Tabula. I used Tabula, because from what I read Camelot required more setup.
Cleaning up Tabula's output made the code brittle, so I evaluated doc parsing services. 
- *Docparser* and *Parseur* produced worse output than Tabula. 
- *Nanonets* requires a human to use a GUI to select ranges in a document. 
- *CaptureFast* requires talking to a salesperson to get an API key
- *DocSumo worked fantastic* the output was accurate and it had great features such as confidence measures on each unit of data.
I performed my analysis on the output of Docsumo.


## Classification
The prompt pushed us to lean into LLMs to parse the doc, so I used ChatGPT to identify financial transactions. If we had data it would be better to use supervised learning. It may be worth paying to classify the docs, for example with Plaid Enrich. 

ChatGPT caught most financial transactions but usually misclasified one of the two large transactions. 
With additional context, such as a list of financial institutions or classified list of line items, ChatGPTs performance may improve. 


## Output format
PDF seemed most appropriate.
With more time we'd use the LLM to instead fill out a template so that we'd have an attractive, consistent format. 

## File structure
- docsumo.ipynb contains the code for using Docsumo to parse the doc. 
- analysis.ipynb contains all the logic and pdf generation.
- credit_analysis.pdf is the output.
- If you're curious, tabula.ipynb shows how much munging is required per document type.
