---
title: "Aggregation vs Aggregation"
date: 2022-05-15
draft: false
author: "Dhruv Bodani"
tags:
  - Threshold BLS Signature Aggregation
  - BLS Signature Aggregation
  - BLS Signatures
description: ""
toc: 
---

### Overview

In this article we will see the comparison between **Threshold BLS Signature Aggregation** and just **BLS Signature Aggregation**. First we will see what are exactly each of these and then we will see what are the similarities and differences between each of them.

First let's start with normal BLS Signature Aggregation

### BLS Signature Aggregation

In BLS Signature Aggregation, it aggregates multiple signatures signed using different private keys. Consider signatures as (\\(\sigma_0 , \sigma_1 , ... , \sigma_{n-1}\\)) over group \\(G_2\\), with messages (\\(m_0, m_1, ... , m_{n-1}\\)), then aggregated signature (\\(\sigma\\)) will be:

\\[ \sigma = \sigma_0 + \sigma_0 + ... + \sigma_{n-1} \\]
\\[ \sigma = \sum_{i=0}^{n-1}\sigma_i \\]

where, "+" represents point addition over group \\(G_2\\) and (\\(\sigma_i = H(m_i)g_2^{sk_i}\\)), where \\(H(m)\\) is a hash of message, \\(sk_i\\) is the ith secret key and \\(g_2\\) is the generator of \\(G_2\\).

Likewise public keys can be aggregated as:

\\[ pk_{agg} = \sum_{i=0}^{n-1}pk_i \\]

where \\(pk_i\\) is the ith public key which is a point in group \\(G_1\\)



### Threshold BLS Signature Aggregation

Threshold BLS Signature is the signature obtained from aggregating **Partial Signatures** (\\(\sigma_i\\)) of a message m signed using shared private keys also called shares (\\(sk_i\\)). We define t and n as threshold and total number of participants. Thus only t of n (eg: 3-of-5) are required to generate a threshold aggregated signature (\\(\sigma\\)) .

We can achive this using Shamir Secret Sharing. We first create a private key and then we split it into shares of it. We use Shamir Secret Sharing to split the private key into shares where a threshold of them can reconstruct the private key.

#### Shamir Secret Sharing (SSS)

The way SSS works is that it creates a polynomial of degree t-1 over a finite field \\( F_q \\) such that:

\\[ f(x) = a_0 + a_1x + ... + a_{t-1}x^{t-1}\\]

where \\( a_0 \\) or \\( f(0) \\) is the private key

The n shares (\\(k_0, k_1, ..., k_{n-1}\\)) of the private key are points on this polynomial such that:

\\[ k_i = f(x_i) \\]

The partial signature signed using each share: \\(\sigma_i = H(m)k_i\\). The message m should be same for all partial signatures.

#### Threshold Aggregation

The signature aggregation in this case is similar previous one where we just need to take the sum of all the signatures but there's catch we need to reconstruct final signature using >= t partial signatures. For that we need a method called ***[Lagrange Interpolation](https://en.wikipedia.org/wiki/Shamir%27s_Secret_Sharing#Computationally_efficient_approach)***

Lagrange Interpolation: According to lagrange interpolation, if we have k+1 data points of any polynomial then the polynomial of degree k can constructed as:

\\[ L(x) = \sum_{i=0}^ky_il_i(x) \\]
\\[ l_i(x) = \prod_{0 \leq m \leq k, m \neq i} \frac{x_m - x}{x_m - x_i} \\]

Now we can say that, threshold aggregated signature is:

\\[ \sigma = \sum_{i=0}^{t-1}\sigma_i \prod_{ m = 0 , m \neq i}^{t-1} \frac{x_m}{x_m - x_i} \\]

where RHS is nothing but \\( L(0) \\) with \\(\sigma_i\\) as \\(y_i\\) which is obtained from signing m with private key \\(k_i\\).
Note: The product part in the above equation is in finite field \\(F_q\\) and summation part is a point of group \\(G_2\\).

### Conclusion

Both are different types of BLS aggregation. Both apply summation of signatures to obtain an aggregated signature but differently. Both are on elliptic curve BLS12-381. Both have similar verification techniques, i.e, using pairings.

### References

- https://www.jcraige.com/threshold-bls-signatures
- https://hackmd.io/@benjaminion/bls12-381
- https://crypto.stanford.edu/~dabo/pubs/papers/aggreg.pdf
- https://eprint.iacr.org/2018/483.pdf
- https://datatracker.ietf.org/doc/html/draft-irtf-cfrg-bls-signature-03
- https://eth2book.info/altair/part2/building_blocks/signatures/
