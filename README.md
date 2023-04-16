<a name="readme-top"></a>

<!-- PROJECT SHIELDS -->
<!--
*** I'm using markdown "reference style" links for readability.
*** Reference links are enclosed in brackets [ ] instead of parentheses ( ).
*** See the bottom of this document for the declaration of the reference variables
*** for contributors-url, forks-url, etc. This is an optional, concise syntax you may use.
*** https://www.markdownguide.org/basic-syntax/#reference-style-links
-->
<!-- [![Contributors][contributors-shield]][contributors-url]
[![Forks][forks-shield]][forks-url]
[![Stargazers][stars-shield]][stars-url]
[![Issues][issues-shield]][issues-url]
[![MIT License][license-shield]][license-url]
[![LinkedIn][linkedin-shield]][linkedin-url] -->

<!-- PROJECT LOGO -->
<br />
<div align="center">
  <h1 align="center">finetune-costbenefit</h1>

  <p align="center">
    Finetuning worse GPT models to mimic the best GPT models in terms of cost-benefit analysis.
  </p>
</div>



<!-- TABLE OF CONTENTS -->
<details>
  <summary>Table of Contents</summary>
  <ol>
    <li>
      <a href="#about-the-project">About The Project</a>
      <ul>
        <li><a href="#built-with">Built With</a></li>
      </ul>
    </li>
    <li>
      <a href="#getting-started">Getting Started</a>
      <ul>
        <li><a href="#prerequisites">Prerequisites</a></li>
        <li><a href="#installation">Installation</a></li>
      </ul>
    </li>
    <li><a href="#usage">Usage</a></li>
    <li><a href="#roadmap">Roadmap</a></li>
    <li><a href="#contributing">Contributing</a></li>
    <li><a href="#license">License</a></li>
    <li><a href="#contact">Contact</a></li>
    <li><a href="#acknowledgments">Acknowledgments</a></li>
  </ol>
</details>



<!-- ABOUT THE PROJECT -->
## About The Project

There are multiple models that OpenAI allows users to access. The most famous of which is `Davinci-03`. However, although it is the best performing compared to the other models, it is also the slowest and most expensive. What is possible though, is to identify a specific application of text generation, and fine-tune a “worse” model `Ada` and train it to mimic the behavior and results of the better model. This would be cheaper and faster, but there is a lack of knowledge on this cost-benefit analysis (“fine-tuning `Ada` with *x* examples results in performance *y*% of `Davinci` and costs *z*% of it”). 

We will investigate this cost-benefit analysis by checking out the instruction *"Improve the sentence:"* on generic sentence datasets, and we will work to compare the performance of fine-tuned `Ada` models and `Curie` in this task.

<p align="right">(<a href="#readme-top">back to top</a>)</p>



### Built With

* [![OpenAI][OpenAI]][OpenAI-url]
* [![HuggingFace][HuggingFace]][HuggingFace-url]

<p align="right">(<a href="#readme-top">back to top</a>)</p>



<!-- GETTING STARTED -->
## Getting Started

To get a local copy up and running follow these simple example steps.

### Prerequisites

Jupyter Notebook and a Macbook. Perhaps you can figure it out on Windows or Google Colab.

### Installation

1. Get an API Key at [https://openai.com/](https://openai.com/)
2. Clone the repo
   ```sh
   git clone https://github.com/ngu-khoi/finetune-costbenefit.git
   ```
3. Enter your API key in `.env` (Text me for the `.env` file)
   ```sh
   OPENAI_SECRET_KEY = $OPENAI_SECRET_KEY
   ```
4. Set up API key in environment to run `OpenAI CLI`
   ```sh
   echo "export OPENAI_API_KEY=$OPENAI_SECRET_KEY" >> ~/.zshrc
   source ~/.zshrc
   echo $OPENAI_API_KEY
   ```
5. Install requirements (preferably in a virtual environment which can be created with `python3 -m venv venv` and activated with `source venv/bin/activate`)
   ```sh
   pip install -r requirements.txt
   ```

<p align="right">(<a href="#readme-top">back to top</a>)</p>

### Data

The `GenericsKB` dataset contains 3.4M+ generic sentences about the world, i.e., sentences expressing general truths such as "Dogs bark," and "Trees remove carbon dioxide from the atmosphere." Generics are potentially useful as a knowledge source for AI systems requiring general world knowledge. The `GenericsKB` is the first large-scale resource containing naturally occurring generic sentences (as opposed to extracted or crowdsourced triples), and is rich in high-quality, general, semantically complete statements. Generics were primarily extracted from three large text sources, namely the Waterloo Corpus, selected parts of Simple Wikipedia, and the ARC Corpus. A filtered, high-quality subset is also available in `GenericsKB-Best`, containing 1,020,868 sentences. You can check out the entire dataset at this [Google Drive link](https://drive.google.com/drive/folders/1vqfVXhJXJWuiiXbUa4rZjOgQoJvwZUoT).

For the purpose of this project, we will be using `GenericsKB-Best` which can be downloaded from this [Google Drive link](https://drive.google.com/file/d/12DfIzoWyHIQqssgUgDvz3VG8_ScSh6ng/view?usp=share_link). Save as `data/GenericsKB-Best.tsv`.

<p align="right">(<a href="#readme-top">back to top</a>)</p>



<!-- USAGE EXAMPLES -->
## Usage

### **WARNING: This requires API calls and will cost money. Before running any programs that call to an API, consult me first. In general, I will have handled the synthetic data and fine-tuning on my computer so you can just use the results.**

### Preprocess data
This is to create the training and testing datasets for the models to be fine-tuned on.
1. Preprocess data
   This step is to convert the `.tsv` file into a `.json` file that removes the unnecessary columns and only keeps the sentence column. Completing this step should result in a `data/generics.json` file.
   ```sh
   preprocess_data.ipynb
   ```
2. Split data
   This step is to split the data into train and test sets. Completing this step should result in `1k_ada`, `10k_ada`, and `100k_ada` folders that contain the train and test sets and the `ada` folder that contains the test set.
   ```sh
   split_data.ipynb
   ```

<p align="right">(<a href="#readme-top">back to top</a>)</p>

### Fine-tune models
The GPT models that will be used in this experiment will be the following:
- Normal Ada
- Fine-tuned 1,000 sample Ada
- Fine-tuned 10,000 sample Ada
- Fine-tuned 100,000 sample Ada
- Curie

The parameters will be kept consistent across the models and will be the following:
- `max_tokens`: 256
- `temperature`: 0.7
- `top_p`: 1
- `frequency_penalty`: 0
- `presence_penalty`: 0
- `best_of`: 1

We choose these parameters because they are the default parameters when accessing the GPT models.

1. [Prepare training data](https://platform.openai.com/docs/guides/fine-tuning/prepare-training-data)
    
   **WARNING**: This requires API calls and will cost money. Please be careful when running this step.

   Training data is how you teach `GPT` what you'd like it to say. We will synthesize the training data via the text completion endpoint. Completing this step will result in a `.jsonl` document, where each line is a prompt-completion pair corresponding to a training example. We will be taking the `train` sets from each `ada` folder and create completions pairs via `Curie` to generate the training data for the models to be fine-tuned on. Upon completion of this step, you should have the following files:
   - `data/{MODEL}/openai_training.jsonl`
   - `data/{MODEL}/openai_cleaned.jsonl`

   `openai_training.jsonl` consists of the prompt-completion pairs generated by `Curie` via the text completion API, and `openai_cleaned.jsonl` consists of the prompt-completion pairs that have been cleaned to reflect specific formatting guides and are ready for fine-tuning.

   Creating `1k_ada` training data takes approximately 10 minutes and costs approximately $0.4.

   Creating `10k_ada` training data is forecasted to take 100 minutes and cost approximately $4.00.

   Creating `100k_ada` training data is forecasted 1000 minutes and cost approximately $40.00.

   ```sh
   prepare_training_data.ipynb
   ```
   
2. Fine-tune models
   
   **WARNING**: This requires API calls and will cost money. Please be careful when running this step.

   This step is to fine-tune the models on the train set. There's not much at this step other than running the API to create a fine-tuned model based on the training data we're providing. The commands are located below.
   - Create `1k_ada` model
   ```sh
   openai api fine_tunes.create -t data/1k_ada/openai_cleaned_prepared.jsonl -m ada --suffix "1k_ada"
   ```
   - Create `10k_ada` model
   ```sh
   openai api fine_tunes.create -t data/10k_ada/openai_cleaned_prepared.jsonl -m ada --suffix "10k_ada"
   ```
   - Create `100k_ada` model
   ```sh
   openai api fine_tunes.create -t data/100k_ada/openai_cleaned_prepared.jsonl -m ada --suffix "100k_ada"
   ```

<p align="right">(<a href="#readme-top">back to top</a>)</p>

### Experimentation
This step is to test the models on the test set and evaluate the performances of the models. Performance is determined by how well the `Ada` models can generate sentences that are semantically similar to the `Curie` model.

1. Run the models on the test set
   
   **WARNING**: This requires API calls and will cost money. Please be careful when running this step.

   After completing this step, you should have a `data/{MODEL}/model_results.json` file that contains the sentences generated from testing the `Ada` model on the test set and a `data/{MODEL}/curie_results.csv` file that contains the sentences from testing the `Curie` model on the test set.

   ```sh
   run_models.ipynb
   ```

2. Evaluate performance using semantic similarity

   After completing this step, you should have a `results/{MODEL}/semantic_similarity.csv` file that contains the semantic similarity scores for each sentence generated by the `Ada` model on the test set with the sentence from the `Curie` model on the test set. Completing this step should result in a `results/semantic_similarity.csv` file that contains the semantic similarity scores for each sentence generated by the `Ada` model on the test set with the sentence from the `Curie` model on the test set.

   ```sh
   semantic_similarity.ipynb
   ```

<p align="right">(<a href="#readme-top">back to top</a>)</p>

### Results
The results of the experiment can be found in the `results` folder.
1. `results/semantic_similarity_{MODEL}.csv` contains the semantic similarity scores of the models on the test set.
2. All analysis and visualizations are completed on the `analysis.ipynb` notebook.
   ```sh
    analysis.ipynb
   ```

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- ROADMAP -->
## Roadmap

- [x] Complete training / synthesis of `ada` models
- [x] Fine-tune `ada` model
- [x] Semantically score `ada` model
- [ ] Analyze performance of `ada` model
- [x] Complete training / synthesis of `1k_ada` models
- [x] Fine-tune `1k_ada` model
- [ ] Semantically score `1k_ada` model
- [ ] Analyze performance of `1k_ada` model
- [ ] Complete training / synthesis of `10k_ada` models
- [ ] Fine-tune `10k_ada` model
- [ ] Semantically score `10k_ada` model
- [ ] Analyze performance of `10k_ada` model
- [ ] Complete training / synthesis of `100k_ada` models
- [ ] Fine-tune `100k_ada` model
- [ ] Semantically score `100k_ada` model
- [ ] Analyze performance of `100k_ada` model
- [ ] Do more analysis on the results (e.g. compare performance between fine-tuned models, evaluate cost and time, etc.)
- [ ] Address technical debt in the project
- [ ] Improve documentation
- [ ] Finish writeup

See the [open issues](https://github.com/othneildrew/Best-README-Template/issues) for a full list of proposed features (and known issues).

<p align="right">(<a href="#readme-top">back to top</a>)</p>



<!-- CONTRIBUTING -->
## Contributing

Contributions are what make the open source community such an amazing place to learn, inspire, and create. Any contributions you make are **greatly appreciated**.

If you have a suggestion that would make this better, please fork the repo and create a pull request. You can also simply open an issue with the tag "enhancement".
Don't forget to give the project a star! Thanks again!

1. Clone/Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

<p align="right">(<a href="#readme-top">back to top</a>)</p>



<!-- LICENSE -->
## License

Distributed under the MIT License. See `LICENSE.txt` for more information.

<p align="right">(<a href="#readme-top">back to top</a>)</p>



<!-- CONTACT -->
## Contact

Your Name - [@ngu_khoi](https://twitter.com/ngu_khoi) - khoinguyen@college.harvard.edu

Project Link: [https://github.com/ngu-khoi/finetune-costbenefit](https://github.com/ngu-khoi/finetune-costbenefit)

<p align="right">(<a href="#readme-top">back to top</a>)</p>



<!-- ACKNOWLEDGMENTS -->
## Acknowledgments

Use this space to list resources you find helpful and would like to give credit to. I've included a few of my favorites to kick things off!

* [Google](https://www.google.com/)

<p align="right">(<a href="#readme-top">back to top</a>)</p>



<!-- MARKDOWN LINKS & IMAGES -->
<!-- https://www.markdownguide.org/basic-syntax/#reference-style-links -->
[contributors-shield]: https://img.shields.io/github/contributors/othneildrew/Best-README-Template.svg?style=for-the-badge
[contributors-url]: https://github.com/othneildrew/Best-README-Template/graphs/contributors
[forks-shield]: https://img.shields.io/github/forks/othneildrew/Best-README-Template.svg?style=for-the-badge
[forks-url]: https://github.com/othneildrew/Best-README-Template/network/members
[stars-shield]: https://img.shields.io/github/stars/othneildrew/Best-README-Template.svg?style=for-the-badge
[stars-url]: https://github.com/othneildrew/Best-README-Template/stargazers
[issues-shield]: https://img.shields.io/github/issues/othneildrew/Best-README-Template.svg?style=for-the-badge
[issues-url]: https://github.com/othneildrew/Best-README-Template/issues
[license-shield]: https://img.shields.io/github/license/othneildrew/Best-README-Template.svg?style=for-the-badge
[license-url]: https://github.com/othneildrew/Best-README-Template/blob/master/LICENSE.txt
[linkedin-shield]: https://img.shields.io/badge/-LinkedIn-black.svg?style=for-the-badge&logo=linkedin&colorB=555
[linkedin-url]: https://linkedin.com/in/othneildrew
[product-screenshot]: images/screenshot.png
[OpenAI]: https://img.shields.io/badge/OpenAI-000000?style=for-the-badge&logo=openai&logoColor=white
[OpenAI-url]: https://openai.com/
[HuggingFace]: https://img.shields.io/badge/Hugging%20Face-FF6E00?style=for-the-badge&logo=huggingface&logoColor=white
[HuggingFace-url]: https://huggingface.co/