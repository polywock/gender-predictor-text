
## Author gender classification from text.

### Use at own risk, not well supported/documented project.

Trained on Reddit posts from r/AskMen and r/AskWomen. If I can say so myself, a clever, but abeit lazy way to get labelled data. Training was done on posts directly from those two subreddits, but this introduces its own set of biases. Maybe women who post on r/AskWomen write in a unique style inside of the subreddit, but not outside of it. To rectify this, you could instead find "women" users from the r/AskWomen, but look at their posts outside of r/AskWomen. Ideally, in a subreddit both men and women visit like r/AskReddit. 

To more accurately identify gender, collect more text by the same author and concatenate them. 

|characters|accuracy|examples|
|----|--------|--------|
|below 100|67.67%|49577|
|100 to 200|75.81%|5850|
|200 to 300|82.41%|1302|
|300 to 500|82.92%|603|
|500 to 1000|85.0%|160|
|above 1000|88.24%|17|

## Use 
1. Install [pipenv](https://github.com/pypa/pipenv) and learn how to use it. 
1. Download required dependencies

    `pipenv install` to download required dependencies. 
1. Predict gender from piping in a text file. This should print out a 0 to 1 value. Male if above 0.5, otherwise female. You could consider the 0.4 to 0.6 range as inconclusive. 

    `cat some_text.txt | pipenv run python3 predict.py`

## Train your own model (not required). 
1. Install required developer dependencies. 
    
    `pipenv install --dev`
1. Run `pipenv run python3 collect_posts.py` to download Reddit posts using the PushShift API. This goes on forever until your interrupt the process. I recommend around 50k to 100k posts. The posts are saved in `data/posts.csv` with columns for gender, author, and body. 
1. Run `pipenv run python3 collect_weights.py` to calculate token weights. This looks through all the Reddit posts to generate a map of words with an associated gender score of -1 (feminine) to 1 (masculine) Eg. The token "wife" and "girlfriend" is used much more by men than women, so it will have `> 0` gender score. This is also done for n-pos (n = 5), which is a n-gram of parts of speech. They are saved to `data/npos_weights.json` and `data/token_weights.json`. Both are used for calculating the final features for the model.
1. Run `pipenv run python3 collect_training_data.py` to turn the posts into training data with features and labels. If you let it complete (slow), it generates `data/chunks.npy`, if you interrupt the process, it saves as `data/chunks_timestamp.npy`, which you can rename to chunks.npy.
1. Run `pipenv run python3 collect_model.py` to train the model and to save the trained model weights and biases to `data/model/weights.json` and `data/model/biases.json`.
1. Predict gender by piping in a text file.

    `cat some_text.txt | pipenv run python3 predict.py`
