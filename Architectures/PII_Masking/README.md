# PII Masking

Personally identifiable information (PII) is a textual reference to personal data that could be used to identify an individual. Examples of PII include:
- names
- addresses
- phone numbers
- bank account numbers
- social security numbers
- drivers license numbers

This type of information can be dangerous if exposed, and many compliance regulations restrict the use of PII.

PII masking (sometimes called obfuscation or anonymization) refers to the process of protecting personal data by hiding PII. PII masking allows you to remove PII from text, limiting their exposure and risk, while also allowing you to retain the majority of the text's content and structure. 

There are several forms of PII masking. One strategy is to scramble the letters/digits present in the personal data. Another strategy involves replacing the PII. Our repository makes use of the second strategy. For PII present in text, we will attempt to replace the PII with the corresponding PII 'marker'. For example, we want names to be replaced with the ```[Name]``` marker.

To implement PII masking, we will be making use of Large Language Models (LLMs), which take a text prompt as input, and produce text as output. We will format our prompt to instruct the model to rewrite the original text with masked PII. As with any project involving language models, this process is not perfect. There is still a chance that PII is not masked or that non-PII is masked. However, there are several techniques to limit the chance of these errors. See the prompting sections in each folder for more information.
