# Part-of-Speech tagging

**Formulation and code description:**
</br>
In this project the goal is to assign a part-of-speech to every word in the sentence using 3 types of Bayes Nets: Simple, HMM and MCMC. A fucntion called <i>calculate_probabilities</i> is defined which calculates all the probabilities required for all the 3 models. This function is called in train function. Following probabilities are calculated:</br>
emission: p(W_i/s_i) = c(s_i,w_i)/c(s_i) </br>
transition: p(s_i+1,s_i) = c(s_i+1,s_i)/c(s_i) </br>
initial: p(s_i) = occurrce of s_i in first word of the sentence/length of data. </br>
Here s_i is POS tag and w_i is word. </br>

Starting with the **simple** model, where each observed variable is dependent only on the its own hidden variable, implementing this Bayes net is straight forward. To get the most probable tags for the words of sentence we find the tag which is has maximum probability amongst all the other tags associated with this word. </br>
-for each word </br>
&nbsp;&nbsp;&nbsp;-if w has s_1....s_k tags: </br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-p(s_i/w) = c(w,s_i)/c(w,s_1) + ... + c(w, s_k) </br> 
Here c(w,s_i) is no. of times w/s_i appears in the corpus. </br>
If a word or tag is present in test set but not in train set, then that word is assigned the most occuring tag in the corpus. </br>

Next up is **HMM** which is solved using viterbi algorithm. In this model the observed variable is dependent on it's hidden variable and there is also a dependency of hidden variable  on the hidden variable of the previous observed variable. Since viterbi uses the concept of dynamic programming we have maintained a list which holds a dicitonary, and the dictionary contains the probabilities for all the POS tags for a particular word (this makes column of word). Once, this so called matrix is calcualted we backtrack to get the list of most probable tags for the sentence. Viterbi algorithm has 3 parts.</br>
~ calculate probabilities for the first  column (or first word of the sentence) using initial probabilities and the emission probabilities.</br>
~ calculate probabilities for the rest of the column (or rest of the words in the sentence) using transition probabilities, emission probabilities and state probabilities.</br>
~ Find the maximum probability in the last column and backtrack to get the most probable tags for the sentence, append them into a list and return the list. </br>
**Reference:** https://web.stanford.edu/~jurafsky/slp3/8.pdf

**MCMC** (Monte carlo markov chains) are used for randomizing and creating a sampling space. Here, we start with the data and assume random POS tags for the sentence in the start. Then we start randomizing the POS tags, we use the disctribution and caluculate the probability of each word, given all the other words are tagged. We assign the maximum POS tag for this word using this formula : </br>
For the first word of the sentence probability is calculated as: P(W0)=P(Wi/S0)*P(S0) </br>
For last word of the sentence probability is calculated as: P(Wi/Si)=P(Wi/Si)*P(Si/Si-1)*P(Si-1)*P(Si/S0)*P(S0) </br>
For other words of the sentence probability is calculated as: P(Wi/Si)=P(Wi/Si)*P(Si/Si-1)*P(Si-1) </br>
1000 samples are processed where the values of initial sample is consists noun for every word in a sentence. </br>
For each sample, every word's probability is calculated for all 12 parts of speech tags and the tag that gives highest probability is considered for that word. The tag of this word is then updated in the current sample which is used in next iteration of samples. This is done for 1000 samples and then tag count is stored for every word from the samples. While doing so, first 500 samples are ignored.
The tag having maximum count for a word will be the final tag for that word, and thus final pos tags list will have tags with maximum probability tag value for all the words. </br>

**Log Posterior** is calculated for each of the 3 model using the formula: </br>
posterior: p(t|w) = p(t) * p(w|t)/ p(w) </br>
and later applying log with base 10 to it.
