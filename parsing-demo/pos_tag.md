## Penn Treebank POS tag with NLTK
```python
from nltk.corpus import ptb
print ptb.tagged_sents('WSJ/00/WSJ_0001.MRG')
```
You can get the following result:
```python
[[(u'Pierre', u'NNP'), (u'Vinken', u'NNP'), (u',', u','), (u'61', u'CD'), (u'years', u'NNS'), (u'old', u'JJ'), (u',', u','), (u'will', u'MD'), (u'join', u'VB'), (u'the', u'DT'), (u'board', u'NN'), (u'as', u'IN'), (u'a', u'DT'), (u'nonexecutive', u'JJ'), (u'director', u'NN'), (u'Nov.', u'NNP'), (u'29', u'CD'), (u'.', u'.')], [(u'Mr.', u'NNP'), (u'Vinken', u'NNP'), (u'is', u'VBZ'), (u'chairman', u'NN'), (u'of', u'IN'), (u'Elsevier', u'NNP'), (u'N.V.', u'NNP'), (u',', u','), (u'the', u'DT'), (u'Dutch', u'NNP'), (u'publishing', u'VBG'), (u'group', u'NN'), (u'.', u'.')]]
```
Some demo codes to process the Wall Street Journal corpus of Penn Treebank to get train/dev/test data set.
```python
'''
Read the Penn Treebank 2.0 pos tag data set (WSJ part)
    - 0-18 train
    - 19-21 development
    - 22-24 test
'''
import os
from nltk.corpus import ptb

dir = 'YOUR_PATH/nltk_data/corpora/ptb/WSJ' 

# standard spliting of the WSJ corpus
train_set = range(19)
dev_set = range(19,22)
test_set = range(22,25)

def get_WSJ(logger=None, START=None, UNK=None, threshold=5, train=False, dev=False, test=False):
    '''
    input:
        logger - logger
        START - start lable for sent
        train - training set or not
        dev - development set or not
        test - test set or not
        threshold - threshold of low frequency words which will be replaced with UNK
    output:
        x - list of word lists
        y - list of tag lists
        vocab - vocabulary (list)
        tag_set - tag set (list)
    '''
    x = []
    y = []
    vocab = set()
    tag_set = set()
    token_size = 0
    data_set = None

    low_freq_vocab = set()
    word_freq = {}    # word freq

    if train:
        data_set = train_set
    if dev:
        data_set = dev_set
    if test:
        data_set = test_set
    if logger != None:
        logger.info('reading Penn Treebank data set...')
    else:
        print 'reading Penn Treebank data set...'
    for subdir in os.listdir(dir):
        if os.path.isdir(os.path.join(dir, subdir)):
            if int(subdir) in data_set:
                # print subdir
                for fname in os.listdir(os.path.join(dir, subdir)):
                    sents = ptb.tagged_sents(os.path.join(dir, subdir, fname))
                    for sent in sents:
                        x.append([])
                        y.append([])
                        x[-1].append(START) # START label needs to be added
                        y[-1].append(START) # START label needs to be added
                        for word, tag in sent:
                            if tag != '-NONE-':
                                x[-1].append(word)
                                y[-1].append(tag)
                                vocab.add(word)
                                tag_set.add(tag)
                                token_size += 1

                                if word not in word_freq:
                                    word_freq[word] = 1
                                else:
                                    word_freq[word] += 1

    tag_set = list(tag_set)
    if logger != None:
        logger.info('token size: %d, sent size: %d, tag set size: %d, vocab size: %d' \
            %(token_size, len(x), len(tag_set), len(vocab)))
    else:
        print 'token size: %d, sent size: %d, tag set size: %d, vocab size: %d' \
            %(token_size, len(x), len(tag_set), len(vocab))

    if train:
        for word in word_freq:
            if word_freq[word] <= threshold:
                low_freq_vocab.add(word)
        for sent in x:
            for idx in range(len(sent)):
                if sent[idx] in low_freq_vocab:
                    sent[idx] = UNK
        vocab = vocab - low_freq_vocab
        vocab.add(UNK)
        vocab = list(vocab)
        if logger != None:
            logger.info('vocab (removing low freq words) size: %d, threshold: %d' %(len(vocab), threshold))
        else:
            print 'vocab (removing low freq words) size: %d, threshold: %d' %(len(vocab), threshold)

    return x, y, vocab, tag_set

if __name__ == '__main__':
    x, y, vocab, tag_set = get_WSJ(START='<s>', UNK='<UNK>', train=True)
else:
    pass
```
