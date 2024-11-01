### Installing fastText (Using GitHub Codespaces)

To implement the fastText model for text classification, you'll need to install the fastText library. Follow these steps to download, extract, and build the fastText binaries:

```bash
# Download the fastText library from the official GitHub repository
$ wget https://github.com/facebookresearch/fastText/archive/v0.9.2.zip

# Unzip the downloaded file
$ unzip v0.9.2.zip

# Navigate to the fastText directory
$ cd fastText-0.9.2

# Compile the fastText library
$ make
```
This setup will build the fastText binaries, allowing you to train and test models on your data.

### Resources

All the files used in this project are available in the following Google Drive folder: [Source](https://drive.google.com/drive/folders/1cD2-uDp2vcGfcN5HP-YeQgbW4OSv-MFZ?usp=sharing)

### Description of Original Dataset

The dataset consists of reviews from Amazon. The data span a period of 18 years, including 35 million reviews up to March 2013. Reviews include product and user information, ratings, and a plaintext review.

This dataset was initially compiled and analyzed by J. McAuley and J. Leskovec in their 2013 RecSys paper, "Hidden factors and hidden topics: understanding rating dimensions with review text."

A modified version of the dataset “Amazon Reviews Full Score” was constructed by Xiang Zhang. It is used as a text classification benchmark in the following paper: Xiang Zhang, Junbo Zhao, Yann LeCun. "Character-level Convolutional Networks for Text Classification." Advances in Neural Information Processing Systems 28 (NIPS 2015).

“Amazon Reviews Full Score” dataset is constructed by randomly taking 600,000 training samples (`train.csv`) and 130,000 testing samples (`test.csv`) for each review score from 1 to 5. In total, there are 3,000,000 training samples and 650,000 testing samples.

### Replication of Original Work ([Source](https://www.kaggle.com/datasets/bittlingmayer/amazonreviews))

The fastText supervised learning requires data in the following format: `__label__<X> <Text>`

Where `<X>` is the class name. Here, the classes are `__label__1` and `__label__2`, and there is only one class per row. `__label__1` corresponds to 1 and 2-star reviews, and `__label__2` corresponds to 4 and 5-star reviews. (3-star reviews, i.e., reviews with neutral sentiment, were not included in the original dataset).

The data from “Amazon Reviews Full Score” is stored in two `.txt` files - `train.txt` and `test.txt`, formatted as per the requirements of the fastText supervised learning model. Each line in the file follows this structure: `__label__<X> <Review Title>: <Review Text>`

This preprocessed data enables the implementation of the fastText model for sentiment analysis.

### Implementation (Using GitHub Codespaces)

**To Train:**
```bash
$ ./fasttext supervised -input /workspaces/Project_fastText/train.txt -output model

Read 289M words
Number of words:  5165173
Number of labels: 2
Progress: 100.0% words/sec/thread:  709649 lr:  0.000000 avg.loss:  0.239041 ETA:   0h 0m 0s
```

**To Test:**
```bash
$ ./fasttext test model.bin /workspaces/Project_fastText/test.txt

N       400000
P@1     0.916
R@1     0.916
```

Evaluated the trained model “model.bin” on a sample review taken from Google - *Amazing film, beautiful and tragic. Excellent casting and editing. Another great one from Sean Baker! The ending was the best part, sad and poignant*

**To Predict the Probability:**
```bash
$ ./fasttext predict-prob model.bin /workspaces/Project_fastText/sample_review.txt

__label__2 1.00001
```

The model classified the review as positive `__label__2` with a probability of 1.00001 (near certainty). This classification aligns with the sentiment expressed in the sample review, demonstrating the model's accuracy.

### Construction of New Data ([Source](https://amazon-reviews-2023.github.io))

The new data source includes 571.54 million Amazon reviews collected over 27 years (from May 1996 to September 2023) across 33 different domains. I downloaded the reviews specifically for the Movies_and_TV domain, which contains 17.3 million reviews.

The dataset contains the following fields:

| Field             | Type   | Explanation                                                                                                        |
|-------------------|--------|--------------------------------------------------------------------------------------------------------------------|
| `rating`          | float  | Rating of the product (from 1.0 to 5.0).                                                                           |
| `title`           | str    | Title of the user review.                                                                                          |
| `text`            | str    | Text body of the user review.                                                                                      |
| `images`          | list   | Images that users post after they have received the product. Each image has different sizes.                       |
| `asin`            | str    | ID of the product.                                                                                                 |
| `parent_asin`     | str    | Parent ID of the product. Products with different colors, styles, sizes usually belong to the same parent ID.      |
| `user_id`         | str    | ID of the reviewer.                                                                                                |
| `timestamp`       | int    | Time of the review (unix time).                                                                                    |
| `verified_purchase` | bool | User purchase verification.                                                                                        |
| `helpful_vote`    | int    | Helpful votes of the review.                                                                                       |

The dataset is in `.jsonl` format. I’ve preprocessed it using Python and saved the reviews in a `.txt` file, formatted as per the requirements of the fastText supervised learning model, with each entry containing the content from “title” and “text” fields, with labels assigned based on the "rating" field.

### Results of the Model on New Data

**To Test the Model on New Dataset:**
```bash
$ ./fasttext test model.bin /workspaces/Project_fastText/new_data.txt

N       9117
P@1     0.925
R@1     0.925
```

These results indicate that the model achieved high accuracy on the new dataset, with both precision and recall at 92.5%. This demonstrates the model's ability to generalize well to unseen data and maintain consistent performance across different datasets.
