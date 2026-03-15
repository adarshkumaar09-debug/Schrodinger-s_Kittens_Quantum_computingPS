# Schrodinger-s_Kittens_Quantum_computingPS
This project explores methods for encoding long DNA sequences into quantum circuits using Qiskit. The objective is to investigate how genomic data can be represented efficiently in quantum systems by mapping nucleotide sequences to quantum states.
## Huffman encoding approach
We know as a matter of fact that huffman encdoing produces the most classically optimised compression. So, we can modify the naive approach of 2-bit encoding and use huffman coding to reduce the number of qubits. If we can minimise the number of qubits required (classically) then we can land at a particularly good solution. But the catch is Huffman coding does no good than 2 bit-encoding (in terms of number of qubits taken) when the A, T, G, C characters are equally distributed, although since the circuit is shallow and the gates used are not many, the fidelity is still close to 1.
This approach is good if we have asymmetry in the A, G, T, C distribution.
## FRQI Approach
Kösoglu-Kind et al. (2023) introduce a quantum algorithm for the A,T,G,C sequence comparison in Scientific Reports, addressing the computational limitations of classical systems in analyzing large genomic datasets. Utilizing the Flexible Representation of Quantum Images (FRQI) framework, the method maps genetic sequences onto quantum computers to enable single-letter resolution analysis, offering improved time and space efficiency over traditional string-matching techniques. The number of qubits were only [log2(n)] whereas a classical system would need 2N bits. It maps genetic sequences onto quantum computers using Toffoli and basis gates. However, due to this, the depth reached a tremendous high, and due to which, the fidelity reached astronomical lows in a noisy environment. Even the description of the paper acknowledged it in the end, as now, we quote, "However, due to noise and errors, current quantum systems’ limitations made it impossible to perform a similarity score with an entire gene sequence that can contain millions upon millions of values."
## Encoding in the Phase Approach
Now since we know that AGTC distribution in the DNA sequence is uniform, that is, there is no asymmetry in the distribution of the nitrogen bases, we cannot use one sequence to compress the DNA sequence. It will be no good. But what if we could have a number to represent an entire sequence?
Consider the first 10 characters of the given 50 character sequence: AGTCGTACGT\
Here, let us first concentrate on one of the nitrogen bases: A\
If we try to put 1 in place of A and 0 wherever there is not A in the sequence, we get a *binary number* 1000001000. The corresponding decimal number is 520.\
This numhber has all the information required for the positioning of A in the sequence, and we could store this number in the *phase* of a qubit.
So let us have a qubit: $|\psi(r)> = \frac{1}{\sqrt2}(|0>+e^{i\frac{r\pi}{180}}|1>)$\
The immediate problem? The angle r when put to 520 can be confused for 160 too since 520%360 is 160. So how do we go about solving this problem?\
We do something recursive: We take the initial angle (let it be K).\
SO when we get the first remainder, $r_1 =  K\mod\ 360 $, we have a quotient, $K_1$\
We can do this to $K_1$ again and then on $K_2$ and so on unless the angle becomes within 0 and 360 degrees.\
Let us try to solve this problem generally.\
Consider a polynomial $p_n (x) = \sum_{i=0}^n a_i x^i$, this is also a number that can be written in base x (x is an integer), if we consider the coefficients $a_i$'s\
Now, the maximum of the polynomial value is when the coefficients $a_i$'s are maximum, i.e., $a_i = x -1$. Then, \
$p_n (x)\_{max} = x^{n+1} - 1$\
Let us choose a suitable n, such that: $x^n \leq K < x^{n+1}$\
We need only those many qubits that are required for the coefficients, which is $n+1$\
So, number of qubits required is $= ceil(log_x K)$\
Here for our case $x = 360$\
Total number of qubits required will be $= \sum_X ceil(log_{360} K_X)$ , given X is in `{A, G, T, C}` \
But there is a problem with this.The thing is, when we do some analysis with AM-GM inequality, \
$\frac{K_A + K_G + K_T + K_C}{4} > (K_A K_G K_T K_C)^{\frac{1}{4}}$\
and that since the binary sequences are mutually exclusive, thus $K_A + K_G + K_T + K_C = 2^{l+1} - 1$, where $l$ is the length of the character sequence, \
then total number of qubits, $n = \sum_X ceil(log_{360} K_X)$\
$n < \sum_X log_{360} K_X + 1$\
$n < 4 + log_{360} (K_A K_G K_T K_C)$\
$n < 4 + log_{360} (\frac{2^{l+1} - 1}{4})$\
which simplifies to $n = O(l)$\
So this is again a linear scaling and we do not have much advantage. The scaling factor fo $n$ is $\frac{ln2}{ln360}$, which means we can have the qubit number as around 12 % of the length of the character sequence.
## Amplitude Encoding approach
Instead of encoding that number in the phase we thought of doing so in the amplitude. But here we face two major problems: 
1. Even if we encode the numbers as the amplitudes of the standard basis of the two qubit system, there is a normalisation factor that we cannot handle
2. In the process of normalising and then decoding on the basis of statistical sampling a lot of sampling errors is introduced in the measured amplitudes, which renders it no good.
