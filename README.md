# VAE-Performance-Gaussian-Mixture-Models--NN4
Analysis of VAE Performance on Gaussian Mixture Models.

# Introduction

Variational Autoencoders (VAEs) have become a fundamental tool in generative modeling, allowing us to learn complex data distributions in an unsupervised manner. While VAEs are commonly applied to high-dimensional data like images, analyzing their behavior on simpler distributions can provide valuable insights into their strengths and limitations.

In this report, we investigate how well a VAE with dense layers can capture the underlying structure of a synthetically generated 3-dimensional Gaussian Mixture Model (GMM) with 10 components. By comparing the VAE-generated samples to the ground truth distribution and analyzing the latent space structure, we aim to assess:

1. How effectively the VAE captures the multimodal nature of the GMM  
2. Whether the VAE's latent space organization reflects the true component structure  
3. If we can accurately estimate the number of mixture components from the latent representation  

# Methodology

## Data Generation

We generated a synthetic dataset from a 3D Gaussian Mixture Model with the following parameters:

- 10 components with randomly generated means within a \[-10, 10\] range in each dimension  
- Diagonal covariance matrices with variances between 0.5 and 2.0  
- Random mixture weights generated from a Dirichlet distribution  
- 10,000 total samples  

An example of the synthetic data obtained is:

*(image placeholder)*

## VAE Architecture

The implemented VAE uses fully-connected layers instead of CNNs:

- **Encoder:** Two hidden layers (3→128→128) with ReLU activations, followed by linear projections to the latent mean and log-variance (128→2)  
- **Latent dimension:** 2 (for easier visualization)  
- **Decoder:** Two hidden layers (2→128→128→3) with ReLU activations  

## Training and Evaluation

The VAE was trained for 100 epochs using the Adam optimizer with a learning rate of 1e-3. We used the standard VAE loss function combining reconstruction loss (MSE) and KL divergence.

To evaluate mode coverage, we:

1. Generated 10,000 samples from the trained VAE  
2. Fitted GMMs with varying numbers of components (5–15) to the generated samples  
3. Used the Bayesian Information Criterion (BIC) to determine the optimal number of components  
4. Visualized both the original and generated samples in 3D space  
5. Examined the 2D latent space organization  

# Results and Analysis

## Mode Capture and Distribution Matching

The true number of GMM components is 10, but after applying our analysis methods, **14 components** were detected, giving a mode coverage of **140%**. This suggests that the VAE is not merely underfitting the data but rather subdividing some of the original clusters into multiple components.

The loss values provide insight into this behavior:

- **Reconstruction loss:** Decreased from 2.5483 to 1.8898 over 100 epochs  
- **KL divergence:** Started at 3.9426 and ended at 3.1580  

This indicates that the model is learning to reconstruct the data points accurately but is still struggling with the distributional constraints imposed by the latent space.

The overestimation of components likely stems from:

- Limited capacity of the 2D latent space, which forces the model to create more complex encoding patterns  
- Gaussian assumptions in the VAE that may not perfectly match the characteristics of each component  
- Potential fragmentation during the clustering process used to count modes  

This overestimation can be seen as a form of compensation—since the VAE struggles to map the complex 3D structure into a 2D latent space, it creates additional "sub-modes" to better reconstruct the original data distribution.

## Latent Space Organization

The 2D latent space visualization shows clear clustering that aligns with mixture components, despite reducing from 3D to 2D. The VAE encoder has learned to separate different modes, though not perfectly one-to-one. The 14 detected clusters likely represent preserved components, fragmented ones, and boundary regions. This fragmentation matches the 140% mode coverage and reflects a trade-off between reconstruction accuracy and latent space regularity during training.

## Distribution Density Matching

Beyond simply counting modes, we also examined how well the VAE matched the density of the original distribution. The 3D visualizations showed that:

1. The VAE reproduced the general shape and spread of each captured component  
2. There was some smoothing of the density, with the VAE samples showing less sharp boundaries between components  
3. Low-density regions between components appeared to be slightly over-represented in the VAE samples  

This smoothing effect is expected behavior for VAEs, which tend to produce slightly "averaged" or blurred versions of the true data distribution due to the Gaussian assumptions in both the latent space and the reconstruction.

## Using T-SNE to Visualize the GMM Samples

Lastly, using T-SNE we wanted to see how well the GMM are formed and analyze if they could be easily split.  

This image corresponds to a representation of the synthetic data. As you can observe, it is difficult to separate some of the clusters while others are clearly different. From this view, we could count 7 different groups but 10 should appear.

*(image placeholder)*

On these two other representations, we can see the VAE-generated samples (left) and its representation in the 2D latent space. We could observe some similarities from the synthetic original data but only slightly. From the 2D latent space, we can observe the different clusters found and transformed from 3D to 2D.

*(image placeholders)*

# Conclusions

Our analysis demonstrates that a VAE with dense layers can model a 3D Gaussian Mixture Model effectively, though with interesting characteristics in how it represents the underlying structure. Rather than missing modes (which would result in <100% coverage), our VAE exhibits a different behavior—fragmenting some clusters to achieve better reconstruction within its constrained latent space.

## Key Findings:

- **Overestimation of modes:** The VAE detected 140% of the original modes, suggesting fragmentation of some clusters  
- **Informative latent structure:** The 2D latent space shows clear clustering that reflects a modified version of the original structure  
- **Density smoothing:** The VAE produces a slightly smoothed version of the original density distribution  

These results highlight both the adaptability of VAEs in modeling multimodal distributions and their specific limitations when working with restricted latent dimensions. The model makes intelligent compromises to capture the data structure, even if that means reinterpreting the number of underlying components.

Overall, this investigation provides a clearer understanding of VAE behavior on multimodal distributions and demonstrates why careful analysis of mode coverage is essential when working with dimensionality reduction techniq


