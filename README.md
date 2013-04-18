# Reclassifier

Reclassifier is a gem that provides string classification via Naïve Bayes classification and Latent Semantic Indexing classification.
It is a fork of the original [Classifier](https://github.com/cardmagic/classifier) gem, which appears to be unmaintained as of a couple of years ago.

## Installation

Add this line to your application's Gemfile:

    gem 'reclassifier'

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install reclassifier

## Dependencies

Currently you need to install the GNU GSL library in order to use Reclassifier: http://www.gnu.org/software/gsl

## Usage

### Bayes
Bayesian Classifiers are accurate, fast, and have modest memory requirements.

#### Usage
    require 'reclassifier'
    b = Reclassifier::Bayes.new 'Interesting', 'Uninteresting'
    b.train_interesting "here are some good words. I hope you love them"
    b.train_uninteresting "here are some bad words, I hate you"
    b.classify "I hate bad words and you" # returns 'Uninteresting'
    
    require 'madeleine'
    m = SnapshotMadeleine.new("bayes_data") {
        Reclassifier::Bayes.new 'Interesting', 'Uninteresting'
    }
    m.system.train_interesting "here are some good words. I hope you love them"
    m.system.train_uninteresting "here are some bad words, I hate you"
    m.take_snapshot
    m.system.classify "I love you" # returns 'Interesting'

Using Madeleine, your application can persist the learned data over time.

#### Bayesian Classification

* http://www.process.com/precisemail/bayesian_filtering.htm
* http://en.wikipedia.org/wiki/Bayesian_filtering
* http://www.paulgraham.com/spam.html

### LSI
Latent Semantic Indexing engines are not as fast or as small as Bayesian classifiers, but are more flexible, providing 
fast search and clustering detection as well as semantic analysis of the text that theoretically simulates human learning.

#### Usage
    require 'reclassifier'
    lsi = Reclassifier::LSI.new
    strings = [ ["This text deals with dogs. Dogs.", :dog],
                ["This text involves dogs too. Dogs! ", :dog],
                ["This text revolves around cats. Cats.", :cat],
                ["This text also involves cats. Cats!", :cat],
                ["This text involves birds. Birds.",:bird ]]
    strings.each {|x| lsi.add_item x.first, x.last}
  
    lsi.search("dog", 3)
    # returns => ["This text deals with dogs. Dogs.", "This text involves dogs too. Dogs! ", 
    #             "This text also involves cats. Cats!"]
  
    lsi.find_related(strings[2], 2)
    # returns => ["This text revolves around cats. Cats.", "This text also involves cats. Cats!"]
    
    lsi.classify "This text is also about dogs!"
    # returns => :dog
  
Please see the Reclassifier::LSI documentation for more information. It is possible to index, search and classify
with more than just simple strings. 

#### Latent Semantic Indexing
* http://www.c2.com/cgi/wiki?LatentSemanticIndexing
* http://www.chadfowler.com/index.cgi/Computing/LatentSemanticIndexing.rdoc
* http://en.wikipedia.org/wiki/Latent_semantic_analysis

## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request

## License

This library is released under the terms of the GNU LGPL. See LICENSE for more details.
