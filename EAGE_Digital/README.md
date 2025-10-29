# Probabilistic Data-Driven Forecasting of Subsurface Structures: Insights from the Geology Forecast Challenge

## Introduction
Predicting geological structures ahead of the drill bit remains one of the most significant challenges in subsurface operations. Real-time geosteering requires reliable understanding of the formation geometry to ensure optimal reservoir contact and avoid drilling into non-productive zones. However, the subsurface is inherently uncertain data from seismic, logs, and wells are sparse and only partially constrain the true geological structure. These uncertainties lead to a wide range of possible layer configurations, especially in formations affected by faults, dips, and folding.

The Geology Forecast Challenge [1] was launched to explore data-driven approaches for modeling geological uncertainty. Organized through Kaggle and supported by ROGII Inc., the competition invited participants to build probabilistic models capable of forecasting multiple plausible layer-depth sequences ahead of drilling. It attracted 197 teams, submitting more than 3,500 solutions. These teams represented a wide range of expertise, fostering cross-disciplinary collaboration and innovation. This initiative aims to bridge the gap between academic research and real-world decision support systems in geosteering, benchmarking methods for probabilistic geological forecasting and uncertainty quantification.

In this work, we present a comprehensive overview and analysis of the Geology Forecast Challenge. We describe the competition framework, dataset characteristics, and evaluation methodology, followed by detailed summaries of the top-performing solutions and a comparative discussion of their results. Finally, we highlight key insights into how different data-driven architectures capture geological uncertainty and discuss their implications for real-time geosteering and probabilistic subsurface modeling.

## Method 

The Geology Forecast Challenge was designed as a probabilistic sequence prediction task [2], where participants were asked to forecast subsurface layer deformations along a lateral direction. Given the known segment of a geological horizon (positions 299 to 0), the goal was to predict the next 300 depth values while producing ten diverse realizations that capture geological uncertainty. Each realization represents a plausible continuation of the subsurface layer.

The competition dataset consisted of approximately 12,000 training samples and 3,000 test samples. Each sample represented a one-dimensional geological layer-depth sequence containing 600 positions (299 to +300) and up to ten realizations per case. All sequences were normalized such that Z(0)= 0. This structured dataset enabled the development of data-driven models capable of learning deformation patterns and uncertainty distributions directly from well-defined examples.

Model performance was evaluated using the standard Negative Log-Likelihood (NLL) loss function, commonly used for probabilistic regression tasks [7]. The NLL measures how well a probabilistic model predicts the true data distribution and is defined as:

	NLL = −∑log p(yi | xi,θ),	(1)

where p(yi | xi,θ) is the predicted probability of the true observation yi given the input xi and model parameters θ, and N is the number of samples.

Minimizing NLL encourages models to assign higher likelihood to the observed data while maintaining calibrated uncertainty estimates, balancing prediction accuracy and confidence in a probabilistic framework.

The first-place solution adopted a purely data-driven strategy that emphasized large-scale training and ensemble consistency. The dataset was expanded through interpolation and splitting to generate over 314,000 samples, with around 210,000 used per training run due to memory limits. The model discarded the geology_id feature and standardized both inputs and outputs to stabilize optimization. Its neural architecture (figure 1) used positional encoding combined with a one-dimensional convolutional front-end (kernel size 7) to capture local geological continuity, followed by a multi-head self-attention layer for long-range dependencies. Residual connections [3], normalization, and a compact feedforward network provided structural stability. The model optimized a custom weighted MSE loss equivalent to the competition’s NLL metric and employed five-fold training with multi-run averaging to improve robustness. This approach yielded highly stable forecasts with minimal numerical drift between realizations.
 
Figure 1 Architecture of the first winning solution, featuring a convolutional front-end with positional encoding and multi-head self-attention for capturing both local and global geological features.

The second-place solution followed a hybrid LSTM–Attention [4] architecture (figure 2) designed to balance accuracy with uncertainty calibration. A dataset of roughly 40,000 samples was constructed using a modified interpolation script, with missing values replaced by zeros. The training loop applied AdamW [6] optimization, cosine-annealing warm restarts, and gradient clipping to prevent exploding gradients. Data augmentation via Mixup [5] (α = 0.4) helped improve generalization. The model combined sequential LSTM layers with multi-head attention and residual skip connections, allowing it to handle both local stratigraphic variations and long-range spatial dependencies. Predictions from five folds were averaged for final inference, and batch-wise normalization of outputs and targets enhanced stability. This architecture produced consistent results while maintaining diversity among the ten predicted realizations.

The third-place solution emphasized simplicity and reproducibility over complexity. It relied on a single deep LSTM network trained with standardized inputs and extended epochs to improve convergence. Despite the lack of attention mechanisms or ensembling, the model achieved competitive accuracy and smooth geological continuation. Its deterministic behavior and minimal parameterization made it attractive for operational workflows where transparency and reproducibility are prioritized.

## Results

The Geology Forecast Challenge demonstrated how different data-driven strategies can capture subsurface uncertainty from limited 1D information. The first, second, and third winning solutions are compared in Figure 3. The black dashed line represents the true geological boundary, while the blue, orange, and green curves correspond to the predictions from the first-, second-, and third-place models, respectively. The gray lines show the closest reference curves from the training data, providing context for how closely each solution aligns with realistic geological structures. The first winning solution delivered the most accurate forecasts, achieving the lowest overall negative log-likelihood and producing
smooth, geologically consistent predictions that closely follow the reference horizon.

 
Figure 2 Architecture of the second winning solution, combining LSTM and multi-head attention layers to capture both local stratigraphic patterns and long-range geological dependencies.

The second and third winning solutions provided a broader spread of realizations, with the second model showing a better balance between accuracy and variability, and the third maintaining interpretability with minimal computational complexity. Figure 4 visualizes the corresponding uncertainty cones for the three solutions. The shaded areas represent the uncertainty magnitude associated with each prediction, which increases with distance from the known data.

Across all solutions, common strategies included input–output scaling, training data augmentation, and ensemble or fold-based averaging to stabilize predictions. These methods collectively improved robustness and reduced overfitting, demonstrating that probabilistic, data-driven approaches can effectively quantify and manage geological uncertainty in forecasting tasks.

Collectively, the findings demonstrate that data-driven models can effectively represent geological deformation patterns when trained on sufficient examples and evaluated probabilistically. High-accuracy ensemble models favor precision, while sequence-aware designs achieve better uncertainty calibration. Together, these findings support integrating probabilistic forecasting into real-time geosteering workflows, where both prediction reliability and uncertainty quantification are critical for informed decisionmaking.

## Conclusions
The Geology Forecast Challenge demonstrated that geological forecasting remains a complex but rewarding problem. The competition showed how open, data-driven collaboration can accelerate innovation in probabilistic subsurface modeling and geosteering. By combining geoscience expertise with modern machine learning, participants developed models that capture both geological realism and predictive uncertainty.

The results confirm that deep learning approaches can effectively forecast geological layer deformations and quantify uncertainty when trained on representative datasets. Open challenges such as this foster reproducibility, cross-disciplinary learning, and community-driven progress—turning diverse solutions
 
### Figure 3 
Comparison of the first-, second-, and third-winning solutions. The black dashed line represents the true solution; blue, orange, and green curves correspond to the three models; and gray lines show the closest reference curves from the training data. into shared insight.

### Figure 4 
Uncertainty cones corresponding to the first-, second-, and third-winning solutions. The shaded regions represent uncertainty magnitude, which increases with distance from the known data.
 
Looking forward, the next steps include extending probabilistic forecasting toward improved integration with geonavigation systems, conducting ablation studies to identify key components, and evaluating how forecast quality translates into better real-time decision-making. Ultimately, geological forecasting supported by data science enables more systematic, transparent, and efficient decision processes in drilling and subsurface operations.

## Acknowledgements
The authors acknowledge ROGII Inc. for sponsoring the Geology Forecast Challenge and providing the geological field data. 
The organizers also thank all contributors and participants who shared their models and insights, enabling collaborative progress in this work.
This work is part of of the project DISTINGUISH (Decision support using neural networks to predict geological uncertainties when geosteering), funded by Aker BP, Equinor, and the Research Council of Norway (RCN PETROMAKS2 project no. 344236).

## References

[1]	Alyaev, S., Blaser, N., Fedorov, A. and Kuvaev, I. [2025] Geology Forecast Challenge. https://kaggle.com/competitions/geology-forecast-challenge-open. Kaggle.
[2]	Boyd, A. [2024] On the Efficient Marginalization of Probabilistic Sequence Models.
[3]	He, K., Zhang, X., Ren, S. and Sun, J. [2015] Deep Residual Learning for Image Recognition.
[4]	Hochreiter, S. and Schmidhuber, J. [1997] Long Short-Term Memory. Neural Comput., 9(8), 1735–1780.
[5]	Jin, X., Zhu, H., Li, S., Wang, Z., Liu, Z., Tian, J., Yu, C., Qin, H. and Li, S.Z. [2025] A Survey on Mixup Augmentations and Beyond.
[6]	Kingma, D.P. and Ba, J. [2017] Adam: A Method for Stochastic Optimization.
[7]	Zhu, D., Yao, H., Jiang, B. and Yu, P. [2018] Negative Log Likelihood Ratio Loss for Deep Neural Network Classification.

