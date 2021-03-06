# TreeHMM 

## Table of Contents

* [Concepts Background](#concepts-background)
  * [What is a Hidden Markov model(HMM)?](#what-is-a-hidden-markov-modelhmm)
  * [Why a Hidden Markov model(HMM)?](#why-a-hidden-markov-modelhmm)
  * [What is a tree?](#what-is-a-tree)
  * [What is a Poly-tree?](#what-is-a-poly-tree)
  * [What is a Tree Hidden Markov model(TreeHMM)?](#what-is-a-tree-hidden-markov-modeltreehmm)
  * [What is forward-backward algorithm?](#what-is-forward-backward-algorithm)
  * [What is baum-welch algorithm?](#what-is-baum-welch-algorithm)
* [Getting Started](#getting-started)
     * [Prerequisites](#prerequisites)
        * [Create a virtual environment](#create-a-virtual-environment)
        * [Activate the virtual environment](#activate-the-virtual-environment)
    * [Installation](#installation)
* [Package Documentation](#package-documentation)
  * [initHMM.py](#initHMMpy)
  * [backward_sequence_generator.py](#backward_sequence_generatorpy)
  * [forward_sequence_generator.py](#forward_sequence_generatorpy)
  * [backward.py](#backwardpy)
  * [forward.py](#forwardpy)
  * [baumWelch.py](#baumwelchpy)
* [License](#license)
* [Contributors](#contributors)
* [Acknowledgments](#acknowledgments)



[](){:name='concepts-background'}
## <a name="concepts-background"></a> Concepts Background {#concepts-background}
### What is a Hidden Markov model(HMM)?

* Hidden Markov models (HMMs) are a formal foundation for making probabilistic 
models of linear sequence. They provide a conceptual 
toolkit for building complex models just by drawing an intuitive picture. 
They are at the heart of a diverse range of programs, including genefinding, 
profile searches, multiple sequence alignment and regulatory site identification. 
HMMs are the Legos of computational sequence analysis.

<img src="https://github.com/harshrai96/TreeHMM/raw/master/Readme_images/HMM.png" width="600">

### Why a Hidden Markov model(HMM)?

* There are lots of cases where you can't observe the states you are interested in but you
can see the effect of being in the state. The observed effect of being in the state is called "emissions" or "observations".

### What is a tree?
In graph theory, a tree is an undirected graph in which any two vertices are connected by exactly one path, or equivalently a connected acyclic undirected graph. 
Tree represents the nodes connected by edges. It is a non-linear data structure. It has the following properties.

* One node is marked as Root node.
* Every node other than the root is associated with one parent node.
* Each node can have an arbitrary number of child node.

### What is a Poly-tree?

* A poly-tree is simply a directed acyclic graph whose underlying undirected graph is a tree.

### What is a Tree Hidden Markov model(TreeHMM)?
* It is the same as an HMM but where the states are linked via a (poly)tree structure rather than a simple path. 

<img src="https://github.com/harshrai96/TreeHMM/raw/master/Readme_images/Rooted_tree.png" width="600">
<img src="https://github.com/harshrai96/TreeHMM/raw/master/Readme_images/Polytree_hmm.png" width="600">


### What is forward-backward algorithm?
* The Forward–Backward algorithm is the conventional, recursive, efficient
 way to evaluate a Hidden Markov Model, that is, to compute the probability of 
 an observation sequence given the model. This probability can be used to classify
 observation sequences in recognition applications.
 The goal of the forward-backward algorithm is to find the conditional 
 distribution over hidden states given the data.
 
 <img src="https://github.com/harshrai96/TreeHMM/raw/master/Readme_images/Forward.png" width="600">
 
 <img src="https://github.com/harshrai96/TreeHMM/raw/master/Readme_images/Backward.png" width="600">

### What is baum-welch algorithm?

* The Baum-Welch algorithm is a dynamic programming approach and a 
special case of the expectation-maximization algorithm (EM algorithm).
The purpose is to tune the parameters of the HMM, namely the state 
transition matrix, the emission matrix, and the initial state distribution,
such that the model is maximally like the observed data.

## Getting Started

### Prerequisites 
#### Create a virtual environment 

* At it's core, the main purpose of Python virtual environments is to create an isolated environment for Python projects. This means that each project can have it's own dependencies, regardless of what dependencies every other project has.

* On macOS and Linux:

        python3 -m venv envname
        
* On Windows:

        py -m venv envname        
#### Activate the virtual environment

* On macOS and Linux:

        source env/bin/activate
* On Windows:

        .\env\Scripts\activate

### Installation

        pip install treehmm
    

# Package Documentation

This is an extension to normal HMMs where the states are connected via a
poly-tree structure rather than simply a path. Moreover, upon available data, 
some of the hidden states can be observed, and hence, can be estimated more 
accurately."
This package is based on a R package and is 40% faster than it.

There are six python files of interest.

* initHMM.py
* backward_sequence_generator.py
* forward_sequence_generator.py
* backward.py
* forward.py
* baumWelch.py


## initHMM.py 

### Description 

* Initializing treeHMM with given parameters

### Usage 

* def initHMM(states, emissions, adjacent_symmetry_matrix, initial_probabilities=None, state_transition_probabilities=None, emission_probabilities=None):

### Arguments

* states : It is a numpy array with first element being discrete state value for the cases(or positive) and second element being discrete state value for the controls(or negative) for given treeHMM.
* emissions: It is a list of numpy array consisting discrete values of
            emissions(where "M" is the possible number of emissions) for each
            feature variable
* adjacent_symmetry_matrix: It is the Adjacent Symmetry Matrix that
            describes the topology of the tree
* initial_probabilities: It is a numpy array of shape (N * 1) containing
            initial probabilities for the states, where "N" is the possible
            number of states(Optional). Default is equally probable states
* state_transition_probabilities: It is a numpy array of shape (N * N)
            containing transition probabilities for the states, where "N" is the
            possible number of states(Optional).
* emission_probabilities: It is a list of numpy arrays of shape (N * M)
            containing emission probabilities for the states, for each feature
            variable(optional). Default is equally probable emissions
	    
### Returns
        
* hmm: A dictionary consisting of above initialised parameters of treeHMM

### Examples 

    sample_tree = np.array([0, 0, 1, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 1, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]).reshape(5, 5)
    states = ['P', 'N']
    emissions = [['L', 'R']]
    hmm = initHMM.initHMM(states, emissions, sample_tree)


## backward_sequence_generator.py 

### Description

* Calculates the order in which nodes in the tree should be traversed during the backward pass(leaves to roots).
* Tree is a complex graphical model where we can have multiple parents and multiple children for a node. Hence the order in which the tree should be tranversed becomes significant. Backward algorithm is a dynamic programming problem where to calculate the values at a node, we need the values of the children nodes beforehand, which need to be traversed before this node. This algorithm outputs a possible(not unique) order of the traversal of nodes ensuring that the childrens are traversed first before the parents

### Usage 

* def backward_sequence_generator(hmm, number_of_levels=100):

### Arguments

* hmm: It is a dictionary given as output by initHMM.py file
* number_of_levels: No. of levels in the tree, if known. Default is 100

###  Returns

* backward_tree_sequence: A list of size "D", where "D" is the number of nodes in the tree

### Examples

    sample_tree = np.array([0, 0, 1, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 1, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]).reshape(5, 5)
    states = ['P', 'N']
    emissions = [['L', 'R']]
    hmm = initHMM.initHMM(states, emissions, sample_tree)
    backward_tree_sequence = backward_sequence_generator.backward_sequence_generator(hmm)

## forward_sequence_generator.py  

### Description 

* Calculate the order in which nodes in the tree should be traversed during the forward pass(roots to leaves) 
* Tree is a complex graphical model where we can have multiple parents and multiple children for a node. Hence the order in which the tree should be tranversed becomes significant. Forward algorithm is a dynamic programming problem where to calculate the values at a node, we need the values of the parent nodes beforehand, which need to be traversed before this node. This algorithm outputs a possible(not unique) order of the traversal of nodes ensuring that the parents are traversed first before the children.

### Usage 

* def forward_sequence_generator(hmm, number_of_levels=100):

### Arguments

* hmm: It is a dictionary given as output by initHMM.py file
* number_of_levels: No. of levels in the tree, if known. Default is 100

###  Returns

* forward_tree_sequence: A list of size "D", where "D" is the number of nodes in the tree

### Examples

    sample_tree = np.array([0, 0, 1, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 1, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]).reshape(5, 5)
    states = ['P', 'N']
    emissions = [['L', 'R']]
    hmm = initHMM.initHMM(states, emissions, sample_tree)
    forward_tree_sequence = forward_sequence_generator.forward_sequence_generator(hmm)

## backward.py : 

### Description 

* Infer the backward probabilities for all the nodes of the treeHMM

### Usage

* def backward(hmm, observation, backward_tree_sequence, observed_states_training_nodes = None):

### Arguments

* hmm: It is a dictionary given as output by initHMM.py file
* observation: observation is a list of list consisting "k" lists for "k" features, each vector being a character series of discrete emission values at different nodes serially sorted by node number
* backward_tree_sequence: It is a list denoting the order of nodes in which the tree should be traversed in backward direction(from leaves to roots).It's the output of backward_sequence_generator function.
* observed_states_training_nodes: (Optional) It is a (L * 2) dataframe where L is the number of training nodes where state values are known. First column should be the node number and the second column being the corresponding known state values of the nodes.

### Returns

* backward_probs: A dataframe of size (N * D) denoting the backward probabilities at each node of the tree, where "N" is possible no. of states and "D" is the total number of nodes in the tree

### Details

* The backward probability for state X and observation at node k is defined as the probability of observing the sequence of observations e_k+1, ... ,e_n under the condition that the state at node k is X. That is: b[X,k] := Prob(E_k+1 = e_k+1,... ,E_n = e_n | X_k = X) where E_1...E_n = e_1...e_n is the sequence of observed emissions and X_k is a random variable that represents the state at node k

### Examples 

    sample_tree = np.array([0, 0, 1, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 1, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]).reshape(5, 5)  # for "X" (5 nodes) shaped tree
    states = ['P', 'N']  # "P" represent cases(or positive) and "N" represent controls(or negative)
    emissions = [['L', 'R']]  # one feature with two discrete levels "L" and "R"
    hmm = initHMM.initHMM(states, emissions, sample_tree)
    observation = [["L", "L", "R", "R", "L"]]
    backward_tree_sequence = backward_sequence_generator.backward_sequence_generator(hmm)
    data = {'node': [1], 'state': ['P']}
    observed_states_training_nodes = pd.DataFrame(data=data, columns=["node", "state"])
    backward_probs = backward.backward(hmm, observation, backward_tree_sequence, observed_states_training_nodes)

## forward.py  

It consists of two functions
* noisy_or
* forward

## noisy_or : Calculating the probability of transition from multiple nodes to given node in the tree

### Description 
* Calculating the probability of transition from multiple nodes to given node in the tree

### Usage 
* def noisy_or(hmm, prev_state, cur_state):

### Arguments 
* hmm: It is a dictionary given as output by initHMM.py file
* previous_state: It is a numpy array containing state variable values for the previous nodes
* current_state: It is a string denoting the state variable value forcurrent node

### Returns:
* transition_prob: The Noisy_OR probability for the transition

### Examples 

    sample_tree = np.array([0, 0, 1, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 1, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]).reshape(5, 5)  # for "X" (5 nodes) shaped tree
    states = ['P', 'N']  # "P" represent cases(or positive) and "N" represent controls(or negative)
    emissions = [['L', 'R']]  # one feature with two discrete levels "L" and "R"
    hmm = initHMM.initHMM(states, emissions, sample_tree)
    transition_prob = forward.noisy_or(hmm,states,"P") # for transition from P & N simultaneously to P

## forward : Infer the forward probabilities for all the nodes of the treeHMM

### Description 

* forward calculates the forward probabilities for all the nodes

### Usage

* def forward(hmm, observation, forward_tree_sequence, observed_states_training_nodes = None):

### Arguments

* hmm: It is a dictionary given as output by initHMM.py file
* observation: observation is a list of list consisting "k" lists for "k" features, each vector being a character series of discrete emission values at different nodes serially sorted by node number
* forward_tree_sequence: It is a list denoting the order of nodes in which the tree should be traversed in backward direction(from leaves to roots).It's the output of backward_sequence_generator function.
* observed_states_training_nodes: (Optional) It is a (L * 2) dataframe where L is the number of training nodes where state values are known. First column should be the node number and the second column being the corresponding known state values of the nodes.

### Returns

* forward_probs: A dataframe of size (N * D) denoting the forward probabilities at each node of the tree, where "N" is possible no. of states and "D" is the total number of nodes in the tree

### Details

* The forward probability for state X up to observation at node k is defined as the probability of observing the sequence of observations e_1,..,e_k given that the state at node k is X. That is: f[X,k] := Prob( X_k = X | E_1 = e_1,..,E_k = e_k) where E_1...E_n = e_1...e_n is the sequence of observed emissions and X_k is a random variable that represents the state at node k

### Examples 

    sample_tree = np.array([0, 0, 1, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 1, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]).reshape(5, 5)  # for "X" (5 nodes) shaped tree
    states = ['P', 'N']  # "P" represent cases(or positive) and "N" represent controls(or negative)
    emissions = [['L', 'R']]  # one feature with two discrete levels "L" and "R"
    hmm = initHMM.initHMM(states, emissions, sample_tree)
    observation = [["L", "L", "R", "R", "L"]]
    forward_tree_sequence = forward_sequence_generator.forward_sequence_generator(hmm)
    data = {'node': [1], 'state': ['P']}
    observed_states_training_nodes = pd.DataFrame(data=data, columns=["node", "state"])
    forward_probs = forward.forward(hmm, observation, forward_tree_sequence, observed_states_training_nodes)


## baumWelch.py 

It consists of two functions
* baumWelchRecursion
* hmm_train_and_test

## baumWelchRecursion : Implementation of the Baum Welch Algorithm as a special case of EM algorithm

### Description

* hmm_train_and_test function recursively calls this function to give a final estimate of parameters for tree HMM. Should not be used directly.

### Usage

* def baumWelchRecursion(hmm, observation, observed_states_training_nodes=None, observed_states_validation_nodes=None):

### Arguments

* hmm: It is a dictionary given as output by initHMM.py file
* observation: observation is a list of list consisting "k" lists for "k" features, each vector being a character series of discrete emission values at different nodes serially sorted by node number
* observed_states_training_nodes: (Optional) It is a (L * 2) dataframe where L is the number of training nodes where state values are known. First column should be the node number and the second column being the corresponding known state values of the nodes
* observed_states_validation_nodes: (Optional) It is a (L * 2) dataframe where L is the number of validation nodes where state values are known. First column should be the node number and the second column being the corresponding known state values of the nodes

### Returns
* newparam: A dictionary containing estimated Transition and Emission probability matrices

### Examples

    sample_tree = np.array([0, 0, 1, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 1, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]).reshape(5, 5)  # for "X" (5 nodes) shaped tree
    states = ['P', 'N']  # "P" represent cases(or positive) and "N" represent controls(or negative)
    emissions = [['L', 'R']]  # one feature with two discrete levels "L" and "R"
    hmm = initHMM.initHMM(states, emissions, sample_tree)
    data = {'node': [1], 'state': ['P']}
    observed_states_training_nodes = pd.DataFrame(data=data, columns=["node", "state"])
    data1 = {'node' : [2,3,4], 'state' : ['P','N','P']}
    observed_states_validation_nodes = pd.DataFrame(data = data1,columns=["node","state"])
    newparam = baumWelch.baumWelchRecursion(copy.deepcopy(hmm),observation,observed_states_training_nodes, observed_states_validation_nodes)

## hmm_train_and_test : Inferring the parameters of a tree Hidden Markov Model via the Baum-Welch algorithm

### Description

* For an initial Hidden Markov Model (HMM) with some assumed initial parameters and a given set of observations at all the nodes of the tree, the Baum-Welch algorithm infers optimal parameters to the HMM. Since the Baum-Welch algorithm is a variant of the Expectation-Maximisation algorithm, the algorithm converges to a local solution which might not be the global optimum. Note that if you give the training and validation data, the function will message out AUC and AUPR values after every iteration. Also, validation data must contain more than one instance of either of the possible states

### Usage

* def hmm_train_and_test(hmm, observation, observed_states_training_nodes=None, observed_states_validation_nodes=None, maxIterations=50, delta=1e-5, pseudoCount=0):


### Arguments

* hmm: It is a dictionary given as output by initHMM.py file
* observation: observation is a list of list consisting "k" lists for "k" features, each vector being a character series of discrete emission values at different nodes serially sorted by node number
* observed_states_training_nodes: (Optional) It is a (L * 2) dataframe where L is the number of training nodes where state values are known. First column should be the node number and the second column being the corresponding known state values of the nodes
* observed_states_validation_nodes: (Optional) It is a (L * 2) dataframe where L is the number of validation nodes where state values are known. First column should be the node number and the second column being the corresponding known state values of the nodes
* maxIterations: It is the maximum number of iterations in the Baum-Welch algorithm. Default is 50
* delta: Additional termination condition, if the transition and emission matrices converge, before reaching the maximum number of iterations(code{maxIterations}). The difference of transition and emission parameters in consecutive iterations must be smaller than code{delta} to terminate the algorithm. Default is 1e-5.
* pseudoCount: Adding this amount of pseudo counts in the estimation-step of the Baum-Welch algorithm. Default is zero

### Returns

* learntHMM:  A dictionary of three elements, first being the infered HMM whose representation is equivalent to the representation in initHMM, second being a list of statistics of algorithm and third being the final state probability distribution at all nodes.


### Examples

    sample_tree = np.array([0, 0, 1, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 1, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]).reshape(5, 5)  # for "X" (5 nodes) shaped tree
    states = ['P', 'N']  # "P" represent cases(or positive) and "N" represent controls(or negative)
    emissions = [['L', 'R']]  # one feature with two discrete levels "L" and "R"
    hmm = initHMM.initHMM(states, emissions, sample_tree)
    data = {'node': [1], 'state': ['P']}
    observed_states_training_nodes = pd.DataFrame(data=data, columns=["node", "state"])
    data1 = {'node' : [2,3,4], 'state' : ['P','N','P']}
    observed_states_validation_nodes = pd.DataFrame(data = data1,columns=["node","state"])
    learntHMM = baumWelch.hmm_train_and_test(copy.deepcopy(hmm),observation,observed_states_training_nodes, observed_states_validation_nodes)



## License 
Distributed under the GNU General Public License v3.0. See `LICENSE` for more information

## Contributors
* [Harsh Kumar Rai (Guest Scientist)](https://github.com/harshrai96) -- Author 
* [Pouria Ramazi (UAlberta)](https://www.math.ualberta.ca/~mlewis/people/pramazi.htm) -- Supervisor
* [Russel Greiner (UAlberta)](https://apps.ualberta.ca/directory/person/rgreiner) -- Supervisor

## Acknowledgments
I want to acknowledge the [R package](https://cran.r-project.org/web/packages/treeHMM/index.html) of Tree HMM by Prajwal Bende from which this package is inspired.




