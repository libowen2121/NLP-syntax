## Some demos of Stanford Parsing
### preparation
1. syntax paring
* constituency parsing
* dependency parsing: CoNLL Dependency, Stanford Dependency, Universal Dependency
2. Stanford Parsing tools
* Stanford Parser: download the [Stanford Parser](https://nlp.stanford.edu/software/lex-parser.shtml#Download), unzip it and unzip ```stanford-parser-X.X.X-models.jar``` in the directory; download the neural dependency parser [model file](https://nlp.stanford.edu/software/stanford-srparser-2014-10-23-models.jar), put it in the directory of Stanford Parser and unzip it.
* Stanford CoreNLP: download the [CoreNLP](https://stanfordnlp.github.io/CoreNLP/download.html) and unzip it.
### Demo code with NLTK
```python
import os
from nltk.parse import stanford

stanford_parser_dir = 'YOUR_PATH/stanford-parser-full-2014-10-31/'
core_nlp_dir = 'YOUR_PATH/stanford-corenlp-full-2017-06-09/'
os.environ['STANFORD_PARSER'] = stanford_parser_dir + 'stanford-parser.jar'
os.environ['STANFORD_MODELS'] = stanford_parser_dir + 'stanford-parser-3.5.0-models.jar'

# constituency parsing
# stanford parser version 3.5.0 2014-10-31
print 'constituency parsing'
dep_parser = stanford.StanfordParser(model_path=stanford_parser_dir + \
    'edu/stanford/nlp/models/lexparser/englishPCFG.ser.gz')
sentences = dep_parser.parse(('The quick brown fox jumps over the lazy dog.',))
# sentences = parser.parse(('bring me a red ball',))
for sentence in sentences:
    print sentence

# dependency parsing (PCFG parser)
# stanford parser version 3.5.0 2014-10-31
print 'PCFG dependency parsing'
dep_parser = stanford.StanfordDependencyParser(model_path=stanford_parser_dir + \
    'edu/stanford/nlp/models/lexparser/englishPCFG.ser.gz')
print[parse.tree() for parse in dep_parser.raw_parse('the quick brown fox jumps over the lazy dog')]

res = list(dep_parser.parse('the quick brown fox jumps over the lazy dog'.split()))
for row in res[0].triples():
    print row
    
# dependency parsing (neural parser)
# model
#   edu/stanford/nlp/models/parser/nndep/PTB_Stanford_params.txt.gz (English, Stanford Dependencies)
#   edu/stanford/nlp/models/parser/nndep/PTB_CoNLL_params.txt.gz (English, CoNLL Dependencies)
#   edu/stanford/nlp/models/parser/nndep/english_UD.gz (default, English, Universal Dependencies)
# executive java file
#   edu.stanford.nlp.parser.nndep.DependencyParser
print 'neural dependency parsing'
print 'stanford dependency'
os.environ['STANFORD_CORENLP'] = core_nlp_dir
neural_dep_parser = stanford.StanfordNeuralDependencyParser(model_path=stanford_parser_dir +
                                                   'edu/stanford/nlp/models/parser/nndep/PTB_Stanford_params.txt.gz',
                                                   java_options='-mx3g')
res = list(neural_dep_parser.parse('the quick brown fox jumps over the lazy dog'.split()))
for row in res[0].triples():
    print row

# print [list(parse.triples()) for parse in dep_parser.raw_parse('The
# quick brown fox jumps over the lazy dog.')]

print 'conll dependency'
neural_dep_parser = stanford.StanfordNeuralDependencyParser(model_path=stanford_parser_dir +
                                                   'edu/stanford/nlp/models/parser/nndep/PTB_CoNLL_params.txt.gz',
                                                   java_options='-mx3g')
res = list(neural_dep_parser.parse('the quick brown fox jumps over the lazy dog'.split()))
for row in res[0].triples():
    print row

print 'universal dependency'
neural_dep_parser = stanford.StanfordNeuralDependencyParser(model_path=stanford_parser_dir +
                                                   'edu/stanford/nlp/models/parser/nndep/english_UD.gz',
                                                   java_options='-mx3g')
res = list(neural_dep_parser.parse('the quick brown fox jumps over the lazy dog'.split()))
for row in res[0].triples():
    print row


```
