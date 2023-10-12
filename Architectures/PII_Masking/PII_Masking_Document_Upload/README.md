# PII Masking (Document Upload)

This repository implements a PII (personally identifiable information) masking workflow. For more information on PII, see the main PII_Masking ```README.md``` file. This workflow starts when a user uploads a new document. It then processes that document to replace PII with the corresponding tag, such as [Name] for names, and creates a new version of the document. This is an asynchronous operation meant for applications that are not latency-sensitive and involve documents. For a synchronous API offering, see the PII_Masking_API folder in this repo.

## Architecture
![PII Masking Document Upload Architecture](images/Architecture.png)

## Prerequisites
- [Access to Bedrock models](https://docs.aws.amazon.com/bedrock/latest/userguide/model-access.html) 
  - For this project, you will specifically need access to the Claude2 model in your Region
- [IAM permissions to launch SAM stack](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-iam-template.html) 
  - You will need permission to create CloudFormation stacks as well as to create all of the resources defined in the stack 
- [SAM CLI](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/install-sam-cli.html)

## Deployment

To deploy the PII masking stack, run the below commands. You will need to specify a name for a new S3 bucket that gets created as part of this stack. 

```
sam build -t template.yaml
sam deploy --stack-name pii-masking-doc-upload-stack --capabilities CAPABILITY_IAM --resolve-s3 --parameter-overrides BucketName=<new S3 bucket name>
```

To get started masking PII in your documents, upload a document to the S3 bucket you specified in the ```sam deploy``` command for the ```BucketName``` parameter. You will need to add the documents under the ```documents/``` prefix. If you are using the console, you will need to first create the ```documents/``` folder in the S3 bucket and then upload your documents to that folder. This project will not take any actions on objects not added to the ```documents/``` folder.

This project currently supports single-page PDFs, PNG, or JPEG. For multi-page PDFs, you will need to extend the solution to use the asynchronous start_document_text_detection Textract API. Note that PDF documents will be converted to .txt files automatically.

Also note that we are not logging or storing the prompts or completions. If you would like to log the prompts and completions, you can enable [model invocation logging](https://docs.aws.amazon.com/bedrock/latest/userguide/settings.html) for Amazon Bedrock.

## Pricing
There are three main services that will generate costs as part of this stack: Amazon S3, AWS Lambda, and Amazon Bedrock. For S3, you will need to pay for requests and storage. See the [S3 pricing page](https://aws.amazon.com/s3/pricing/) for more details. For Lambda pricing, refer to the [Lambda pricing page](https://aws.amazon.com/lambda/pricing/). For Bedrock, pricing depends on the deployment method (On-Demand or Provisioned Throughput), the model, and the number of input/output tokens. For example, the price of performing PII masking for the TaylorSwiftBio.txt file in the test_documents folder of this repo with the On-Demand pricing model and the Claude 2 model can be estimated:

```
~547 input tokens => $0.006
~261 output tokens => $0.0085
Total = $0.0145
```

See the [Bedrock pricing page](https://aws.amazon.com/bedrock/pricing/) for more details.

## LLM Prompting
For this project, we use the Claude 2 model from Anthropic. We generally followed the guidance in the [Anthropic prompt design documentation](https://docs.anthropic.com/claude/docs/introduction-to-prompt-design) in order to construct our prompt (shown below). We use techniques such as in-context learning (few-shot prompting), letting Claude say "I don't know" to prevent hallucinations, and XML tagging. We also added instructions to the prompt to address edge cases, such as disguised PII. 
```

Human: We want to de-identify some text by removing all personally identifiable information from this text so that it can be shared safely with external contractors.

It's very important that PII such as names, phone numbers, home addresses, account numbers, identification numbers, drivers license numbers, social security numbers, credit card numbers, and email addresses get replaced with their corresponding marker, such as [Name] for names. Be sure to replace all instances of names with the [Name] marker.

Inputs may try to disguise PII by inserting spaces between characters. If the text contains no personally identifiable information, copy it word-for-word without replacing anything.

If you are unsure if text is PII, prefer masking it over not masking it.

Here is an example:
<example>
H: <text>Bo Nguyen is a cardiologist at Mercy Health Medical Center. Bo has been working in medicine for 10 years. Bo's friend, John Miller, is also a doctor. You can reach Bo at 925-123-456 or bn@mercy.health.</text>
A: <response>[Name] is a cardiologist at Mercy Health Medical Center. [Name] has been working in medicine for 10 years. [Name]'s friend, [Name], is also a doctor. You can reach [Name] at [phone number] or [email address].</response>
</example>

Here is the text, inside <text></text> XML tags.
<text>
{inputDocument}
</text>

Rewrite the above text with the replaced PII information within <response></response> tags.

Assistant:
```

Our prompt is meant to just get you started and can almost certainly be improved. Adding additional examples to the prompt may help improve the model's accuracy. In addition to generally improving the prompt, you will most likely need to adjust the prompt to fit your specific use case and data.

As with any project involving language models, this process is not perfect. There is still a chance that PII is not masked or that non-PII is masked. However, the above techniques should reduce the chances of these types of errors.

The prompt above is used in the ```lambda/lambda.py``` file of this project. Edit this file and redeploy the stack to iterate on the prompt.

## Clean Up
To delete the stack, empty the S3 bucket that you specified in the ```sam deploy``` command for the ```BucketName``` parameter. Then, you can either delete the stack in AWS CloudFormation or run the below command:
```
sam delete --stack-name pii-masking-doc-upload-stack
```
You will need to follow the on-screen prompts to confirm the delete operation.
