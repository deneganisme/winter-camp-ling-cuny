True-casing with a hidden Markov model (aka "Winter Camp")
==========================================================

Learning goals
--------------

In this exercise, you will learn about a simple but important NLP task called
*true-casing* or *case\_restoration*, which allows us to

-   restore missing capitalization in noisy user-generated text as is often
    found in text messages (SMS) or posts on social media, or even
-   add capitalization to the output of [machine
    translation](https://en.wikipedia.org/wiki/Machine_translation) and [speech
    recognition](https://en.wikipedia.org/wiki/Speech_recognition) to make them
    easier for humans to read, or even
-   transfer the "style" of casing from one collection of documents to another.

As part of this exercise, you will build your own case restoration system using
Python and command-line tools. As such you will get practice using Python to
read and writing text files, and using the command line.

Prerequisites
-------------

You should have a working familiarity with [Python 3](https://www.python.org/)
and be somewhat comfortable using data structures like lists and dictionaries,
calling functions, opening files, importing built-in modules, and reading
technical documentation.

While you need not be familiar with [hidden Markov
models](https://en.wikipedia.org/wiki/Hidden_Markov_model), one of the
technologies we use, it may be useful to learn a bit about this technology
before beginning. Hidden Markov models are covered in detail by many textbooks
in speech and language processing, including Jelinek 1997 (chap. 2) and
Eisenstein 2019 (chap. 7).

The exercise is intended to take several days; at the [Graduate
Center](https://www.gc.cuny.edu/Page-Elements/Academics-Research-Centers-Initiatives/Doctoral-Programs/Linguistics/Linguistics),
master's students in computational linguistics often complete it as a
supplemental exercise over winter break, after a semester of experience. (Hence
the name "Winter Camp.")

### Software requirements

This tutorial assumes you have access to a UNIX-style command line interface:

-   On Windows 10, you access a command line using [Windows Subsystem for
    Linux](https://docs.microsoft.com/en-us/windows/wsl/install-win10); the
    Ubuntu distributions are particularly easy to use.
-   On Mac OS X, you can access the command line interface by opening
    Terminal.app.

It also assumes that you have Python 3.6 or better installed. To test, run
`python --version` from the command line, and note the version number that it
prints. If this returns an error, you probably don't have Python installed yet.
One easy way to obtain a current version of Python is to install
[Anaconda](https://docs.anaconda.com/anaconda/install), a free software package.
Note that if you're using Anaconda from within Windows Subsystem for Linux, you
will want to install the Linux version, not the Windows version.

Case restoration
----------------

Nearly all speech and language technologies work by collecting statistics over
huge collections of characters and/or words. While handful of words (like *the*
or *she*) are very frequent, the vast majority of words (like *ficus* or
*cephalic*) are quite rare. One of the major challenges in speech and language
technology is making informed predictions about the linguistic behaviors of rare
words.

Many [writing systems](https://en.wikipedia.org/wiki/Writing_system), including
all of those derived from the Greek, Latin, and Cyrillic alphabets, distinguish
between upper- and lower-case words. Such writing systems are said to be
*bicameral*, and those which do not make these distinctions are said to be
*unicameral*. While casing can carry important semantic information (compare
*bush* vs. *Bush*), this distinction also can introduce further "sparsity" to
our data. Or as Church (1995) puts it, do we **really** need to keep totally
separate statistics for *hurricane* or *Hurricane* or can we merge them?

In most cases, speech and language processing systems, including machine
translation and speech recognition engines, choose to ignore casing
distinctions; they
[`casefold`](https://docs.python.org/3/library/stdtypes.html#str.casefold) the
data before training. While this is fine for many applications, it is often
desirable to restore capitalization information afterwards, particularly if the
text will be consumed by humans.

Lita et al. (2003) introduce a task they call "true-casing". They use a simple
machine learning model, a [*hidden Markov
models*](https://en.wikipedia.org/wiki/Hidden_Markov_model), to predict the
capitalization patterns of sentences, word by word. They obtain good overall
accuracy (well about 90% accurate) when applying this method to English news
text.

The exercise
------------

The exercise is divided into six parts.

Part 1: the paper
-----------------

Read [Lita et al. 2003](https://www.aclweb.org/anthology/P03-1020/), the study
which introduces the true-casing task. If you encounter unfamiliar jargon, look
it up, or ask a colleague or teacher.

**TODO**: how can we encourage learning at this stage?

### Part 2: data and software

**TODO**: get some relevant data (maybe a year of the WMT data linked below?),
tokenize it, and install HunPos. Provide very detailed instructions for this.

### Part 3: training

**TODO**: convert to two-column format and call `hunpos-train`. Also introduce
`case.py` and talk about how it works.

### Part 4: prediction

**TODO**: call `hunpos-tag` and convert back to tokenized format.

### Part 5: evaluation

So, how good your true-caser? There are many ways we can imagine measuring this.
One could ask humans to rate the quality of the output casing, and one might
even want to take into account how often two humans agree about whether a word
should or should not be capitalized. However a simpler evaluation (and one which
does not require humans "in the loop") is to compute accuracy at the token
level. Accuracy is simply the probability individual tokens being
correctly-cased, and can be computed by dividing the number of correctly-cased
tokens by the number of total tokens,
[`round`](https://docs.python.org/3/library/functions.html#round)ed to 3-6
decimal places.

While it is possible to compute accuracy directly on the tags produced by
`hunpos-tag`, this has the risk of slightly underestimating the actual accuracy.
For instance, if the system tags a punctuation character as `UPPER`, this seems
wrong, but it is harmless; token is inherently case-less and it doesn't matter
what kind of tag it receives. When multiple predictions all give the right
"downstream" answer, the model is said to exhibit *spurious ambiguity*; a good
evaluation method should not penalize spurious ambiguity. In this case, one can
avoid spurious ambiguity by evaluating not on the tags but on the tokenized
data, after it has been converted back to that format.

**TODO**: describe the structure of `evaluate.py`.

### Part 6: style transfer

**TODO**: apply to some data from social media.

Postscript
----------

### Further reading

Other interesting work on case-restoration includes Chelba & Acero 2006 and Wang
et al. 2006.

### Stretch goals

1.  Above, the act of training the model and casing data required several
    commands. It may be more convenient to combine the steps into two programs,

-   a "trainer" which converting tokenized data to two-column format and invokes
    `hunpos-train` on it, and
-   a "predictor" which converts tokenized data to one-column format, invokes
    `hunpos-tag`, and then converts it back to tokenized format. The obvious way
    to do this is to write two Python scripts which, as part of their process,
    call the HunPos shell commands using the built-in
    [`subprocess`](https://docs.python.org/3/library/subprocess.html) module. In
    particular, use
-   [`subprocess.check_call`](https://docs.python.org/3/library/subprocess.html#subprocess.check_call),
    which executes a shell command and raises an error if it fails, or
-   [`subprocess.popen`](https://docs.python.org/3/library/subprocess.html#popen-constructor),
    which has a similar syntax but also captures the output of the command.
    Since both these scripts must generate temporary files (i.e., the data in
    one- or two-column format) you may want to use the built-in
    [`tempfile`](https://docs.python.org/3/library/tempfile.html) module, and in
    particular
    [`tempfile.NamedTemporaryFile`](https://docs.python.org/3/library/tempfile.html#tempfile.NamedTemporaryFile).
    Since these scripts will need to take various arguments (paths to input and
    output files, as well as the model order hyperparameters), use the built-in
    [`argparse`](https://docs.python.org/3/library/argparse.html) module to
    parse command-line flags.

2.  Convert your implementation to a [Python
    package](https://packaging.python.org/) which can be installed using `pip`.
    If you are also doing stretch goal \#1, you may want to make the trainer and
    the predictor separate ["console
    scripts"](https://python-packaging.readthedocs.io/en/latest/command-line-scripts.html#the-console-scripts-entry-point).
    Make sure to fail gracefully if the user doesn't already have HunPos
    installed.
3.  Train and evaluate a model on a language other than English (though make
    sure the writing system makes case distinctions---not all do); even French
    and German both have rather different casing rules...
4.  Train, evaluate, and distribute a **gigantic** case restoration model using
    a megacorpus such as

-   [Gigaword](https://catalog.ldc.upenn.edu/LDC2011T07),
-   the [Billion Word
    Benchmark](https://github.com/ciprian-chelba/1-billion-word-language-modeling-benchmark),
    or
-   the [WMT news
    crawl](https://gist.github.com/kylebgorman/5109b09fbfc3a2c1dbbdd405326c1130).

5.  One limitation of HMMs and (and generative models in general) is that they
    only support rather simple features based on nearby words and tags. In
    comparison, discriminative sequence models allow users to specify arbitrary
    emission features (though they still impose stringent restrictions on
    transition features). The
    [`perceptronix`](https://github.com/kylebgorman/perceptronix/) library
    provides a fast C++-based backend for training linear sequence models with
    the perceptron learning algorithm. Install Perceptronix, then using
    `case.py` and the `perceptronix.SparseDenseMultinomialSequentialModel`
    class, build a discriminative case restoration engine and compare it to the
    HMM model.

References
----------

Chelba, C. and Acero, A.. 2006. Adaptation of maximum entropy capitalizer:
little data can help a lot. *Computer Speech and Language* 20(4): 382-39.

Church, K. W. 1995. One term or two? In *Proceedings of the 18th Annual
International ACM SIGIR conference on Research and Development in Information
Retrieval*, pages 310-318. Seattle.

Eisenstein, J. 2019. *Introduction to natural language processing*. Cambridge:
MIT Press.

Jelinek, F. 1997. *Statistical methods for speech recognition*. Cambridge: MIT
Press.

Lita, L. V., Ittycheriah, A., Roukos, S. and Kambhatla, N. 2003. tRuEcasIng. In
*Prooceedings of the 41st Annual Meeting of the Association for Computational
Linguistics*, pages 152-159. Sapporo, Japan.

Wang, W., Knight, K., and Marcu, D. 206. Capitalizing machine translation. In
*Proceedings of the Human Language Technology Conference of the NAACL, Main
Conference*, pages 1-8, New York.