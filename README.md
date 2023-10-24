# Amazon Bedrock Architectures

Generative AI applications are powered by large machine learning models called Foundation Models (FMs). These models are capable of generating unique content such as text, images, videos, etc. While these models are very powerful, they should be *part* of the solution, not the entire solution. These models serve as the 'engine' for generating content, but need to be used in conjuction with other tools such as databases, front-ends, orchestration tools, storage services, etc in order to form a fully-featured application.

This repository contains example solutions for various Generative AI use cases. The purpose of this repository is to provide a list of use cases to inspire your own use cases, a recommended architecture and deployment template to serve as a starting point for each use case, and example LLM prompts. 

The ```Architectures``` folder contains guidance and sample code for various Generative AI use cases. Each subfolder contains architectures and deployment templates for the use case. These templates are only meant to serve as sample code. You will need to modify these templates for your own real-life use case.

## Contents
- [Architectures](Architectures/)
  - [PII Masking](Architectures/PII_Masking/README.md)
  - [Summarization](Architectures/Summarization/README.md)
  - More coming soon including:
    - Entity extraction
    - Classification/tagging
    - Search
    - Image generation

## Contributing

We welcome community contributions! Please see [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

## License

This library is licensed under the MIT-0 License. See the [LICENSE](LICENSE) file.
