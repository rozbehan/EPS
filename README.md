# Equivalence Projective Simulation 
This code is supposed to simulate the "equivalence projective simulation" model that uses 
[projective simulation (PS)](https://projectivesimulation.org) framework for computationaly 
modeling equivalence class formation in Behavior analysis. 
This code is used for producing results in paper entitled:

**"Equivalence Projective Simulation as a Framework for Modeling Formation of Stimulus Equivalence Classes"**


- Overally, the simulation is to firat train some relations say A1-B1, A2-B2, B1-C1, B2-C2, through a matching-to-sample procedure. Then, after mastery of agent in these relations, i.e. answering correctly to say 90% of the trials in a block of certain size of trials, the agent will be tested.
- The test could be the relations that are trained directly, or some relations that are not explicitly traind, say **derived** relations.
- A protocol is a road map to which relations must be trained, what is the criteria for mastery, what is the testing relations, etc. 
- In the model, the details of protocol must be changed in initialization_detail.py, but more general parameters can be changed in initialization.py 
- We consider different scenarios for agent behavior in the testing phase and how to update the graph weights.
- Interaction between agent and Environment, is by passing a matching-to-sample trial to the agent, passing the agent choice to the Environment, and the feedback/reward -in the training phase- to the agent.  

## Getting Started

### Prerequisites


This code has been tested with Python 3.6.5 (Anaconda)

The Interface is tested on Windows 10

The program might crash for not valid initial values

## Files details

There are two options to initialize the code and run it. 
1- If you run the "main.py", you can change the initial values in initialization.py
2- If you run the "main_gui.py", you can initialize values through an interfce (only Windows OS)

The Initialization_detail.py is for adding new experiments and can not be changed through interface window which means just pre-defined experiments can be chosen. 

**The process is as follows:**

- an agent, an environment, an intreaction, and possibly an interface object will be initializes

- The results to be ploted will be saved in a pickle file in the "results" folder.

- The figures will be shown in new Tabs of interface or new windows.

- Results of a previousely simulated data can be accessed by its ID. Press open in the interface.  


## What initial values means


### For environment:
**experiment_ID**: \
A positive number which is used for saving the results in the results folder. The file name would be: 
'agent_type_Env_environment_ID_ExpID_experiment_ID'
    
**environment_ID**: \
Is the key to the defult ID numbers, that adress the details of a protocol

**max_trial**: \
The maximum number of allowed trials in the training phase. If the learning is so slow due to the parameters, the training might not be finished

**num_agents**: \
The number of participants (agents); The program will be repeated for num_agents times and the final result would be the average

**num_classes**: \
Is the number of classes in the experiment. It must be compatible with training_order

**size_action_set**: \
The number of comparison stimuli in the experiment. This must be at least 2 and at most the num_classes

**training_order**: \
The structure of training and blocks in dict() format. There are two ways to initialize it though:

- First: let num_classes=4, and size_action_set=3, and

     training_order={ \
                     1: [('A', 'B', 40)], \
                     2: [('B', 'C', 40)], \
                     3: [('D', 'C', 40)] \
                     }
     
     The numbers 1,2,3 shows the order of training. As an example, the first block contains 30 trials of relations with A1, A2, A3, or A4 as the sample stimuli and three comparison stimuli from B1, B2, B3, and B4. Note that the correct choice must be among the comparison stimuli, say for A2, (B1, B3, B4) is not a valid action set/comparison stimuli. Moreover that number of trials at each block must be a multiple of num_classes. Since, the model produce the same number of trial for each particular pair. 
 
- Second: let num_classes=2, and size_action_set=2, and

     training_order={ \
                     1:[('A1', 'B1', 10)],\
                     2:[('A2', 'B2', 10)],\
                     3:[('A1', 'B1', 5),('A2', 'B2', 5)],\
                     4:[('A1', 'C1', 10)],\
                     5:[('A2', 'C2', 10)],\
                     6:[('A1', 'C1', 5),('A2', 'C2', 5)],\
                     7:[('A1', 'B1', 2),('A2', 'B2', 2),('A1', 'C1', 2),('A2', 'C2', 2)]\
                    }
                    
     This case, the desired relation to be trained in each block and the number of its repetition is determined. The num_classes must be compatible with the provided relations. The above training_order means after mastery of relation A1-B1 in blocks of 10 trials, A2-B2 will be trained, then a block of mixed A1-B1 and A2-B2. Next, A1-C1, then A2-C2 and then a mixed block of A1-C1, A2-C2. Finally, all the trained relations will make a block and by passing the mastery criteria, the training phase will be finished.   

**testing_order**: \
The structure of testing and blocks in dict() format. It must be similar to the First format of training test, say: 

- 
    testing_order={\
                    1:[('A', 'B', 9),('B', 'C', 9),('D' ,'C', 9)],\
                    2:[('B', 'A', 9),('C', 'B', 9),('C', 'D', 9)],\
                    3:[('A', 'C', 9)],\
                    4:[('C', 'A', 9),('B', 'D', 9), ('D', 'B', 9),('A', 'D', 9),('D', 'A', 9)]\
                   }
                   
    In order to preperation of the results, two other dict() needs to be set in accordance with the training_order:
    - **test_block_ID**: \
    which name the blocks. One sample could be:
    
        {1:'Baseline', 2:'Symmetry', 3:'Transivity',4:'Equivalence'}
    - **plot_blocks**: \
    That is an option for desired combination of relasions, say:
        
        plot_blocks= {\
                        'Direct':['AB', 'BC', 'DC'],\
                        'Derived':['BA', 'CB', 'CD', 'AC', 'CA', 'BD', 'DB', 'AD', 'DA']\
                    }
              
        where 'AB' means all possible relations between the two categories say A1-B1, A2-B2, A3-B3, etc. One must set it as empty dict() to remove this extra plot. 
        
        "plot_blocks_ID": {'relatin_type':['Direct','Derived']}

**mastery_training**: \
A value between 0 and one that shows the mastery criteria. $0.9$ means $90\%$ correct choices in a block.  

### For agent:

**policy_type**: \
Could be ['standard', 'softmax']. The difference is on computation of probabilities from h-values. In 'standard' type, h-values must be positive.

**beta_softmax**: \
Applicable for 'softmax' type. float >=0, probabilities are proportional to exp(beta_softmax*h_value) and finding the appropriate value is very important. In general, Its higher value, increases the chance of an edge with the largest h-value to be chosen. For instance, let the h=[1, 25, 3] be the h-values for three options. In the 'standard' policy the prob=[0.03448276 0.86206897 0.10344828]. In 'softmax' if beta_softmax=0.0, then prob=[0.333333, 0.333333, 0.333333], if beta_softmax=0.1, then prob=[0.07550259, 0.83227834, 0.09221907], if beta_softmax=1, then prob=[3.77513454e-11, 1.00000000e+00, 2.78946809e-10], and if beta_softmax=10, then [5.87928270e-105, 1.00000000e+000, 2.85242334e-096].

**gamma_damping**: \
A float number between 0 and 1 which controls forgetting/damping of h-values. The closser to zero, the less forgetting and the closer to one, the less learning/memory.

**agent_type**: \
Could be ['positive_h','negative_h','viterbi','absorbing']. The most critical part of the model is the way the agent learns derived relations. This is projected into the connection weights (h-values). In the model, the asuumption is these connections are made and dynamically updated in the network during training, and we compute them whenever a trial (test) targeted them. The scenarios that we cover in the model, addressed in the agent_type:

- positive_h: h-values must be positive. New connections are computed based on max-product using Dijekstra algorithm.

- negative_h: h-values are negative. K3, and K4 are not used in training (no indirect reinforcement) and just 'softmax' policy is applicable. The computation of probabilities is similar to the positive-h.

- viterbi: Find the max-product value through a trellis diagram and Viterbi algorithm. First the order of categories that the path with highest probability is passes is find, then extra edges will be removed and probabilities will be computed. So probabilities out of h-values computed in a different way.
- absorbing: The case that derived relations are computed based on a random walk, when action_set form the absorbing states.


**K1**: \
Is a positive value for updating the h-values and the defult value is one based on original PS. In the model K1 could be any positive value

**K2**: \
A positive number which represents the symmetry relation in the model and upper bounded by K1 

**K3**: \
A positive number, uper bounded by where K1/(size_action_set - 1). K3 is used in the negative reward case in order to reinforce other options compare to the wrong choice agent have done. This parameter helps to keep the h-values positive. If the agent_type ='negative_h', K3 and K4 are irrelevant since the h-values can be negative. 

**K4**: \
Similar to K2 for symmetric relations, in the negative reward situation. 

**category**: \
Could be ['True', 'False']. If category='True', The transition probabilities computed as marginal probabilities to each category. For instance, if A-B and A-C are trained, then the sum of output probabilities from A would be 2 in the case of category=True, and one otherwise. The defult value is category=False.

**memory_sharpness**: \
A value between 0 and one which shows that how much the agent uses the memory i.e. navigate through the memory clips and reach an action indirectly. The more intact memory the higher value of mempry_sharpness. Aplicable for 'standard' type and the default value is 1. memory_sharpness=1 means memory is used completely and  memory_sharpness=0 means actions will be random for the derived cases.
    
**nodal_theta**: \
Could be ['Not', 'Lin', 'Pow'] and aplicable for 'standard' type. nodal_theta and gamma_nodal are related to nodal effect and provide options for computing probabilities in derived relations. nodal_theta shows the policy to define theta where it will be used to compute the probabilities. Let the calculated probabilty from a given agent_type be p=[p_1, p_2, p_3] and a random probability (analogous to not using memory/learning) be p_r=[1/3, 1/3, 1/3]. Moreover the D_nodal shows the number of nodes between the percept and the action set. (D_nodal for A and C is one and for A and D is two if the training order is A-B-C-D). Then the final probability will be calculated as 
p*theta + p_r*(1-theta). theta computed then in the following way:

- if nodal_theta = Not: theta=memory_sharpness, i.e. in this case theta does not change with D_nodal
- if nodal_theta = Lin: theta= max(0, memory_sharpness - (D_nodal* gamma_nodal)), i.e. theta changes linearly with nodal distance
- if nodal_theta = Pow: theta= memory_sharpness* pow(D_nodal, -1*gamma_nodal), i.e. theta changes with nodal distance in a power law format

**gamma_nodal**: \
float >= 0 where gamma_nodal=0 means no nodal effect. If, nodal_theta=Lin, for gamma_nodal >= mempry_sharpness/D_nodal, the choices would be random. Closer value to zero, less nodal effect. Aplicable for 'standard' type. 


## Assumptions/Constraints 

- Percept (sample stimulus) and action_set (comparison stimuli) belong to two different categories. i.e. at the current code the **reflexivity** is not tested and the comparison stimuli can not be from different categories (which is pretty standard in desing of matching-to-sample experiments.
 
-  When blocks are constructed by environment, the order of comparison stimuli and the order of trials in the block are random.

- There is two phases in the model, training with feedback and testing without feedback. In real experiments with human subjects during training, the feedback will be reduced to say $75\%$, $50\%$, and $25\%$ and it is to see if the participant remember the relations. In a synthetic and probabilistic model, the argument is not valid and we did not consider this case. 

## Authors

* **Asieh Abolpour Mofrad** 
* **Mahdi Rouzbahan** 

## License


```python

```
