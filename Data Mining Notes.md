## 18.4 Ranking Algorithms

### 18.4.1 Page Rank

- basic idea: highly reputable documents are more likely to be cited (or in-linked) by other reputable Web pages.

- **Random Surfer Model**, models the reputation of a Web page in terms of its long-term frequency of visits by a random surfer

  - The long-term relative frequency of visits to any particular page is clearly influenced by the number of in-linking pages to it
  - the long-term frequency of visits to any page will be higher if it is linked to by other frequently visited (or reputable) pages.
  - Long-term frequency - **steady-state probability**
  - also known as **random walk model**

- Issues with Random Surfer Model: some Web pages may have no outgoing links, which may result in the random surfer getting trapped at specific nodes, known as **dead ends**.

  **Solutions**:

  - The first modification is to add links from the dead-end node (Web page) to all nodes (Web pages), including a self-loop to itself. Each edge has a transition probability of 1/n **(Demo!)**

  - If the dead ends are defined on *groups of nodes*, also known as dead-end component, or absorbing component, then **(Demo!)**

    - the steady-state transitions will stay trapped in these dead-end components.
    - Furthermore, in such cases, the final probability distribution of nodes in various dead-end components is not unique and it is dependent on the starting state.   

    Aside from the addition of these edges, a **teleportation**, or **restart step**is used within the random surfer model. The step is defined as follows.

    At each transition, the random surfer may either jump to an arbitrary page with probability α, or it may follow one of the links on the page with probability (1−α), α with a typical value of 0.1.

    Because of the use of teleportation, the steady state probability becomes unique and independent of the starting state. α is also called as a **smoothing** or **damping probability**.

    Large values of α typically result in the steady-state probability of different pages to become more even.

  <img src="/Users/mingxulu/Documents/Data-Mining-Notes/pictures/screenshot-20210218-164638.png" alt="screenshot-20210218-164638" style="zoom:50%;" />

- How to determine steady-state probability?

  Let $G = (N,A)$ be the **directed** Web graph, in which nodes correspond to pages, and edges correspond to hyperlinks. The total number of nodes is denoted by $n$. It is assumed that A also includes the added edges from dead-end nodes to all other nodes. The set of nodes incident on $i$ is denoted by $In(i)$, and the set of end points of the outgoing links of node $i$ is denoted by $Out(i)$. The steady-state probability at a node $i$ is denoted by $π(i)$. In general, the transitions of a Web surfer can be visualized as a Markov chain, in which an $n*n$ transition matrix $P$ is defined for a Web graph with $n$ nodes. The PageRank of a node $i$ is equal to the steady-state probability $π(i)$ for node $i$, in the Markov chain model. The probability $p_{ij}$ of transitioning from node $i$ to node $j$, is defined as $1/|Out(i)|$. The steady-state probability π(i) of node is the sum of the probability of a teleportation into it and the probability that one i of the in-linking nodes directly transitions into it. The probability of a teleportation into the node is exactly because a teleportation occurs in a step with probability α, and α/n all nodes are equally likely to be the beneficiary of the teleportation. The probability of a transition into node is given by <img src="/Users/mingxulu/Documents/Data-Mining-Notes/pictures/screenshot-20210218-164703.png" alt="screenshot-20210218-164703" style="zoom:50%;" />. Therefore, at steady-state, the probability of a transition into node i is defined by the sum of the probabilities of the teleportation and transition events are as follows:  <img src="/Users/mingxulu/Documents/Data-Mining-Notes/pictures/screenshot-20210218-164714.png" alt="screenshot-20210218-164714" style="zoom:50%;" />. **(Use node 2 as an example)**

  <img src="/Users/mingxulu/Documents/Data-Mining-Notes/pictures/screenshot-20210218-164721.png" alt="screenshot-20210218-164721" style="zoom:50%;" />

  Let π = (π(1) . . . π(n))T be the n-dimensional column vector representing the steady-state probabilities of all the nodes, and let e be an n-dimensional column vector of all 1 values. The system of equations can be rewritten in matrix form as follows:<img src="/Users/mingxulu/Documents/Data-Mining-Notes/pictures/screenshot-20210218-164732.png" alt="screenshot-20210218-164732" style="zoom:50%;" />

  In addition, because the vector π represents a probability, the sum of its components of π must be equal to 1.
  
  Note that this is a linear system of equations that can be easily solved using an iterative method. The algorithm starts off by initializing π(0) = e/n, and it derives π(t+1) from π(t) by repeating the following iterative step: <img src="/Users/mingxulu/Documents/Data-Mining-Notes/pictures/screenshot-20210218-164744.png" alt="screenshot-20210218-164744" style="zoom:50%;" />
  
  - **Power-iteration method**
  
    After each iteration, the entries of π(t+1) are normalized by scaling them to sum to 1. These steps are repeated until the difference between π(t+1) and π(t) is a vector with magnitude less than a user-defined threshold.
  
  PageRank computation is expensive. Rather, the PageRank values for **all the known Web pages** are precomputed and stored away. The stored PageRank value for a page is accessed only when the page is included in the search results for a particular query for use in the final ranking.
  
  
  
  

#### 18.4.1.1 Topic-Sensitive PageRank

- basic idea: it is desired to provide greater importance to some topics than others in the ranking process, also known as also the personalization of ranking values.



#### 18.4.1.2 SimRank

- basic idea: compute the structural similarity between nodes. In other words, the similarity

  between nodes i and j, is the same as that between j and i.