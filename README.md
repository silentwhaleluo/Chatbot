# BIA-66O Chat Robot
Team member:
Jingyu Luo
Dashun Liu

## Project plan
|Task Work|Takeholders|States|
|------|---|---
|Collect & Preprocess Data to Pre-train the Generator|Jingyu Luo|Down|
|Build the Generator model|Jingyu Luo|Down|
|Pre-train the Generator & Adjust the Hyperd-parameters|Jingyu Luo|Down|
||
|Collect & Preprocess Data to Pre-train the Discriminator|Dashun Liu|Doing|
|Build the Discriminator model|Dashun Liu|Down|
|Pre-train the Discriminator & Adjust the Hyperd-parameters|Dashun Liu|Doing|
||
|Collect & Preprocess Data to Pre-train the sequnce GAN (Generative adversarial network)|Jingyu Luo|Doing|
|Build the sequnce GAN Model|Jingyu Luo|Doing|
|Train the sequnce GAN & Adjust the Hyperd-parameters|Dashun Liu|To do|
||
|Correct the the Englishe grammer problem for robot output|Jingyu Luo|To do|
|Build the interface for the chatbot|Dashun Liu|To do|
**Notes: With project processing, new dataset, new model will let the model re-build and re-train many times, and thus the file will change over time and may different**

## Collect & Preprocess Data to Pre-train the Generator
 This generator has two dataset
 1. [Cornell movie-dialogs corpus](https://www.cs.cornell.edu/~cristian/Cornell_Movie-Dialogs_Corpus.html)
 2. [Small dataset for chatbot dialog](https://github.com/bshao001/ChatLearner/tree/master/Data/Corpus/Augment1) from Github
 - **combine the conversation**<br/>
 - **split in 1 question and 1 answer format**<br/><br/>
 ### Steps
1. Form a large enough English unique word dictionary.  File: Form_dictionary.ipynb
 - Create a unique vocabulary according to the English word list from Github from [NLTK WordNet](http://www.nltk.org/howto/wordnet.html)<br/>
 - uppdate the this unique vocabulary with all the traning & testing data<br/><br/>
   > part of unique vocabulary: <br/>\['!', '"', '"...', '"60', '"A', '"All', '"And', '"Be', '"Beloved', '"Bride'\]
 - Create the word to index & index to word dictionary

    >part of word to index dictionary: <br/>{'!': 2,
 '"': 3,
 '"...': 4,
 '"60': 5,
 '"A': 6,
 '"All': 7,
 '"And': 8,
 '"Be': 9,
 '"Beloved': 10,
 '"Bride': 11,
 '"But': 12,
 '"Chronicle"': 13,
 '"Cinderella': 14,
 '"Come': 15,
 '"Dear': 16,
 '"Death': 17,
 '"Do': 18,
 '"Doctor': 19,
 '"Don\'t': 20,
 '"Dracula."': 21}

2. Preprocess cornell movie-dialogs corpus
 - Combine the movie_lines.txt and movie_conversations.txt to form the conversation dataset File: Data_Generator_ConellMovie1.ipynb
##### The structure of original dataset
##### movie_lines.txt:
    - lineID
    - characterID (who uttered this phrase)
    - movieID
    - character name
    - text of the utterance

##### example
    ['L1045 +++$+++ u0 +++$+++ m0 +++$+++ BIANCA +++$+++ They do not!',
     'L1044 +++$+++ u2 +++$+++ m0 +++$+++ CAMERON +++$+++ They do to!',
     'L985 +++$+++ u0 +++$+++ m0 +++$+++ BIANCA +++$+++ I hope so.',
     'L984 +++$+++ u2 +++$+++ m0 +++$+++ CAMERON +++$+++ She okay?',
     "L925 +++$+++ u0 +++$+++ m0 +++$+++ BIANCA +++$+++ Let's go."]
        
##### movie_conversations.txt:
    - characterID of the first character involved in the conversation
    - characterID of the second character involved in the conversation
    - movieID of the movie in which the conversation occurred
    - list of the utterances that make the conversation, in chronological 
        order: ['lineID1','lineID2',É,'lineIDN']
        has to be matched with movie_lines.txt to reconstruct the actual content
##### example
    ["u0 +++$+++ u2 +++$+++ m0 +++$+++ ['L194', 'L195', 'L196', 'L197']",
     "u0 +++$+++ u2 +++$+++ m0 +++$+++ ['L198', 'L199']",
     "u0 +++$+++ u2 +++$+++ m0 +++$+++ ['L200', 'L201', 'L202', 'L203']",
     "u0 +++$+++ u2 +++$+++ m0 +++$+++ ['L204', 'L205', 'L206']",
     "u0 +++$+++ u2 +++$+++ m0 +++$+++ ['L207', 'L208']"]

##### the result file is:
    - conversation_id: same conversation id means that this lines in the same conversation
    - character_id: this is a binary data only have 0 and 1. 0 means one character and 1 means another.
    - text: the text of conversation
 - Pre-process cornell movie-dialogs corpus to pre-train the Generator File: cornell movie-dialogs corpus.ipynb

    - **Preprocess text**<br/>
    - **Using the dictionary transformes the tokens to numbers**<br/>
    - **Build sequence dataset as numpy ndarray format**<br/><br/>


##### Display the pre-process steps
- text preprocess
  > e.g.: He's email adrees is 23@223.com   ==>   <start> he s email adrees is . com <end>
- transfer the tokens sequence to number sequnce according to the word index dictionary we created in Form_dictionary.inpy
    > e.g. The total dataset looks like: <br>
        ([[    6,  2421, 19085, ...,     0,     0,     0],
        [    6, 19152,     3, ...,     0,     0,     0],
        [    6, 11946, 17611, ...,     0,     0,     0],
        ...,
        [    6, 10308,  2847, ...,     0,     0,     0],
        [    6, 19152,  8487, ...,     0,     0,     0],
        [    6,   559,  8487, ...,     0,     0,     0]])
 
2. Preprocess Small dataset for chatbot dialog File: Data_Generator_chatbotDialog.ipynb
 - The process steps are similar to the that of cornell movie-dialogs corpus
 #### The original dataset looks like
>'Q: Good morning !',
 'A: Good morning ! _func_val_client_code_show_picture_randomly_para1_good_morning',
 'Q: How are you doing ?',
 'A: Doing good , thank you . _func_val_ask_howru_if_not_yet',
 'Q: What is your last name ?',
 "A: As a robot , I do n't have a family name .",
 '===',
 'Q: Good afternoon !',
 'A: Good afternoon ! _func_val_client_code_show_picture_randomly_para1_good_afternoon',
 'Q: What is your family name ?',
 "A: As a robot , I do n't have a family name .",
 'Q: How old are you ?',
 'A: I am 9 years old .',
 'Q: Are you a boy or a girl ?',
 'A: I am a boy .',
 '===',













## Build the Generator model
The structure of the Generator model is sequence to sequnce with attention.<br>
The algorithm for this structure is illustrate in the following picture.<br>
The difference is that this picture is the translation model from English to Spanish. However, in our chat robot model, both the input and outputs are English.<br>
The Generator structure can split in two part. Encoder and Decoder.<br>

#### Structure of Encoder: <br>
Encoder Input (question sequence)<br>
↓<br>
Shared-Embedding<br>
↓<br>
gru<br><br>
↓<br>

#### Structure of Encoder:<br>

Decoder Input  (Answer sequence)<br>
↓<br>
Shared-Embedding<br>
↓<br>
Attention Weights<br>
↓<br>
gru<br>
↓<br>
Fully Connected layer ( The multi-class classification, the class number is the number of unique words<br>
↓<br>
Output (The same Encoder data with t+1 time step)<br>
<img src="https://www.tensorflow.org/images/seq2seq/attention_mechanism.jpg" width="500" alt="attention mechanism">
Reference: [Mnt-with-attention](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/contrib/eager/python/examples/nmt_with_attention/nmt_with_attention.ipynb) form tensorflow

## Pre-train the Generator & Adjust the Hyperd-parameters

## Collect & Preprocess Data to Pre-train the Discriminator
Working...
## Build the Discriminator model
Working...
## Pre-train the Discriminator & Adjust the Hyperd-parameters
Working...

## Collect & Preprocess Data to Pre-train the sequnce GAN (Generative adversarial network)
Working...
## Build the sequnce GAN Model
Working...
## Train the sequnce GAN & Adjust the Hyperd-parameters
Working...

## Correct the the Englishe grammer problem for robot output
Working...
## Build the interface for the chatbot
Working...
