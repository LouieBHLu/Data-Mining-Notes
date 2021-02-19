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

  Let π = (π(1) . . . π(n))T be the n-dimensional column vector representing the steady-state probabilities of all the nodes, and let $e$ be an n-dimensional column vector of all 1 values. The system of equations can be rewritten in matrix form as follows:<img src="/Users/mingxulu/Documents/Data-Mining-Notes/pictures/screenshot-20210218-164732.png" alt="screenshot-20210218-164732" style="zoom:50%;" />

  In addition, because the vector π represents a probability, the sum of its components of π must be equal to 1.
  
  Note that this is a linear system of equations that can be easily solved using an iterative method. The algorithm starts off by **initializing π(0) = e/n**, and it derives π(t+1) from π(t) by repeating the following iterative step: <img src="/Users/mingxulu/Documents/Data-Mining-Notes/pictures/screenshot-20210218-164744.png" alt="screenshot-20210218-164744" style="zoom:50%;" />
  
  - **Power-iteration method**
  
    After each iteration, the entries of π(t+1) are normalized by scaling them to sum to 1. These steps are repeated until the difference between π(t+1) and π(t) is a vector with magnitude less than a user-defined threshold.
  
  PageRank computation is expensive. Rather, the PageRank values for **all the known Web pages** are precomputed and stored away. The stored PageRank value for a page is accessed only when the page is included in the search results for a particular query for use in the final ranking.
  

#### 18.4.1.1 Topic-Sensitive PageRank
- basic idea: it is desired to provide greater importance to some topics than others in the ranking process, also known as also the personalization of ranking values. 
- How can this be achieved?
  1. fix a list of base topics, and determine a high-quality sample of pages from each of these topics (use *Open Directory Project*)
  2. The PageRank equations are now modified, so that the teleportation is only performed on this **sample set of Web documents**, rather than on the entire space of Web documents. Let ep be an n-dimensional personalization (column) vector with one entry for each page. An entry in ep takes on the value of 1, if that page is included in the sample set, and 0 otherwise. Let the number of nonzero entries in $\hat{e_p}$ be denoted by $\hat{n_p}$. Then, the PageRank Eq. 18.4 can be modified as follows:<img src="/Users/mingxulu/Documents/Data-Mining-Notes/pictures/screenshot-20210219-095512.png" alt="screenshot-20210219-095512" style="zoom: 67%;" /> The same power-iteration method can be used to solve the personalized PageRank problem. The selective teleportations bias the random walk, so that pages in the structural locality of the sampled pages will be ranked higher.
#### 18.4.1.2 SimRank #TBD
- basic idea: compute the structural similarity between nodes. 
- Symmetric: SimRank determines symmetric similarities between nodesIn other words, the similarity between nodes i and j, is the same as that between j and i.
- 



### 18.4.2 HITS, Hypertext Induced Topic Search

- basic idea: A query-dependent algorithm for ranking pages.
- **Intuition**: an understanding of the typical structure of the Web that is organized into hubs and authorities.
  - **Authority**: a page with many in-links. Typically, it contains authoritative content on a particular subject, and, therefore, many Web users may trust that page as a resource of knowledge on that subject. 
  - **Hub**: provides guidance to Web users about where they can find the resources on a particular topic. Thus, a hub page provides guidance to Web users about where they can find the resources on a particular topic.
  - **Insight**: good hubs point to many good authorities. Conversely, good authority pages are pointed to by many hubs, shown in figure b.s

<img src="/Users/mingxulu/Documents/Data-Mining-Notes/pictures/screenshot-20210219-101201.png" alt="screenshot-20210219-101201" style="zoom:50%;" />

- **HITS algorithm**, starting with the list of relevant pages and expands them with a **hub ranking** and an authority ranking.
  
  - collecting the top-r most relevant results to the search query at hand. A typical value of r is 200. This defines the root set R. For each node in R, the algorithm determines all nodes immediately connected (either in-linking or out-linking) to R. This provides a larger base set S. Because the base set S can be rather large, the maximum number of in-linking nodes to any node in R that are added to S is restricted to k. A typical value of k used is around 50.
  
  - Let G = (S,A) be the subgraph of the Web graph defined on the (expanded) base set S, where A is the set of edges between nodes in the root set S. Each page (node) i ∈ S is assigned both a hub score h(i) and authority score a(i). It is assumed that the hub and authority scores are normalized. Higher values of the score indicate better quality. The hub and authority scores are related to one another in the following way:
  
    <img src="/Users/mingxulu/Library/Application Support/typora-user-images/image-20210219105233856.png" alt="image-20210219105233856" style="zoom:50%;" />
  
    **The basic idea is to reward hubs for pointing to good authorities and reward authorities for being pointed to by good hubs.**
  
    The algorithm starts by initializing $h^0(i) = a^0(i) = 1/\sqrt{|S|}$. Let $h^t(i)$ and $a^t(i)$ denote the hub and authority scores of the $i$th node, respectively, at the end of the $t$th iteration.
  
    For each t ≥ 0, the algorithm executes the following iterative steps in the (t + 1)th iteration:
  
    <img src="/Users/mingxulu/Library/Application Support/typora-user-images/image-20210219105702423.png" alt="image-20210219105702423" style="zoom: 67%;" />
  
    Of course, this process can be expressed in a matrix form. The updates can be expressed as $\vec{a}=A^T\vec{h}$ and $\vec{h}=A\vec{a}$, when the edge set $A$ is treated as an $|S|*|S|$ adjacency matrix. The iteration is repeated to convergence. 



## 18.5 Recommender Systems

- Collect data includes information about user profiles, interests, browsing behavior, buying behavior, and ratings about various items.

- In the recommendation problem, the user–item pairs have **utility values** associated with them. For n users and d items, this results in an n °ø d matrix D of utility values, also referred as the **utility-matrix**. There are two nature of the utility matrix, which has a significant influence on the choice of recommendation algorithm:

  1. **Positive preferences only**

     <img src="/Users/mingxulu/Documents/Data-Mining-Notes/pictures/screenshot-20210219-142634.png" alt="screenshot-20210219-142634" style="zoom:50%;" />

  2. **Positive and negative preferences (ratings)**, more complex but stronger way, a way that users can provide negative preferences.

     <img src="/Users/mingxulu/Documents/Data-Mining-Notes/pictures/screenshot-20210219-142734.png" alt="screenshot-20210219-142734" style="zoom:50%;" />

- **TBD: two kinds of recemmendations and their attributes**

### 18.5.1 Content-Based Recommendations

- The user is associated with a set of documents that describe his or her interests, obtained at redistration time, the product description of the items bought, and so on. These documents can then be aggregated into a single textual content-based profile of the user in a vector space representation.

- When **no** utility matrix, a simple knearest neighbor approach is used to find top-k items that are closest to the user textual profile.

- When utility matrix is available, the problem of finding the most relevant items for a particular user can be viewed as a traditional **classification** problem. 

  For each user, we have a set of **training** documents representing the descriptions of the items for which that user has specified utilities. The **labels** represent the **utility values**. The descriptions of the remaining items for that user can be viewed as the **test** documents for classification. 

  - When utility matrix is numeric (contains numeric ratings), **Logistic** and **ordered probit** regresssion are most popular. 
  - When utility matrix contains only positive preferences, then all utility entries correspond to positive examples for the item. Therefore, the classification is then performed only on the remaining test documents.

- **Advantages**: no need for a utility matrix and leverage domain-specific content information

- **Bias**: content information biases the recommendation towards items described by similar keywords to what the user has seen in the past.

### 18.5.2 Neighborhood-Based Methods for Collaborative Filtering

- **basic idea**: Use either user–user similarity, or item–item similarity to make recommendations from a ratings matrix.

#### 18.5.2.1 User-Based Similarity with Ratings





