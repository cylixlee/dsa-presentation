---
marp: true
headingDivider: 2
paginate: true
math: mathjax
---

<style scoped>
    h1 {
        font-size: 1.25rem;
    }
</style>

# Transferability of White-box Perturbations: Query-Efficient Adversarial Attacks against Commercial DNN Services

<small>Included in USENIX Security 24</small>

## Deep Neural Networks

Deep neural networks have been widely used to extract higher-level features from the raw data and provide users with explicitly desicions.

![](dnn.png)

## Commercial DNN Services

Commercial DNN Services combine the computing power of the cloud with AI to offer business advantages, including quicker processing and cost savings.

<center>
    <img src="commercialdnn.png" width=960>
</center>

Commercial DNN Services often access a large number of devices, and a successful attack against one of them exposes all to potential threats.

## Adversarial Attacks

Adversarial attacks add subtle perturbations to images to deceive the target model.

![](adversarialattack.png)

## Adversarial Attacks against Commercial DNN Services

![](attackagainstcommercial.png)

- **Black box manner**: use APIs to query model and obtain hard-label outputs.
- **Strict query limits**: high costs and time consumption, more subceptible to defense.

## Existing Black-box Attacks

Various attacks have been proposed, which mainly fail into three categories: **transfer-based**, **query-based** and **hybrid**.

| Categories     | Method                                                                                          |
|----------------|-------------------------------------------------------------------------------------------------|
| Transfer-based | MI-FGSM[CVPR'18], DIM[CVPR'19], SINI-FGSM[ICLR'20], FIA[ICCV'21], NAA[CVPR'22]                  |
| Query-based    | BA[ICLR'18], HSJA[S&P'20], AHA[ICCV'21], SurFree[CVPR'21], RamBoAttack[NDSS'22]                 |
| Hybrid         | BiasedBA[ICCV'19], BAODS[NeurIPS'20], QEBA-I[CVPR'20], Prism[AISec'19], HybridAttack[USENIX'20] |

## Drawback of existing Black-box Attacks

<style scoped>
    .horizontal {
        display: flex;
        flex-direction: row;
        justify-content: space-between;
    }
    .vertical {
        display: flex;
        flex-direction: column;
    }
    .margintop {
        margin-top: 2rem;
    }
</style>

<div class="horizontal">
    <div class="vertical">
        <h3>Transfer-based</h3>
        <p>Low success rate</P>
        <h3>Query-based</h3>
        <p>Large number of queries</P>
        <h3>Hybrid</h3>
        <p>Large number of queries, as substitute models cannot be fully exploited.</P>
    </div>
    <div class="vertical margintop">
        <img src="transferbased.png">
        <img src="hybrid.png">
    </div>
</div>

## Goal and Challenges

Goal: **Query-efficient** adversarial attacks against **commercial DNN services**.

Challenges:
- How to **exploit the transferability** of adversarial perturbations to boost black-box attacks?
- How to design an **effective and query-efficient** adversaial attack?

## Transferability of White-box Perturbations
<style scoped>
    p {
        font-size: 0.75rem;
    }
</style>

<img src="region.png" align=right width=550>

### Adversarial region
Adversarial region $O$ denotes that the inside examples can deceive the model.

### Transfer-based attacks
Transfer-based attacks utilize substitute models to craft a candidate adversarial example, but this example often **fails to deceive** the model.

### Dispersed sampling
We aim to sample in the embedding space and craft **multiple examples**. It is more likely to find an example located in $O^\prime \cap O$.

## Transferability of White-box Perturbations (Cont'd)

<img src="justification.png" align=right width=700>

### Justification
By integrating the dispersed sampling technique, the MI-FGSM significantly improves the attack success rate by an average of **9.6%** using only **2.1** average queries.    

## Overview of DSA

4-step attack workflow to exploit the transferability of white-box perturbations.

![](overview.png)

## Module #1 Image Augmentation


### Varying the distribution or magnitude of perturbations enables different transferability.

<style scoped>
    li {
        font-size: 0.85rem;
        margin-left: -20px;
    }
</style>

<img src="module1.png" align=right width=550>

- Carry out **image augmentation** (e.g. rotation, color jittering, and noise addition) on the original image.
- Image augmentation causes a **change** in the gradient of the neural network **backpropagation**, which changes the direction of perturbation optimization.
- White-box attacks typically perform the above optimization **multiple times**, **accumulating** such changes.

## Module #2 Perturbation Constraint Generation

<style scoped>
    .container {
        display: flex;
        flex-direction: row;
        justify-content: space-between;
    }
</style>

### Dynamically establish constraints for each white-box perturbation through a sampling process

<div class="container">
    <img src="module2-left.png" width=525>
    <img src="module2-right.png" width=525>
</div>

- Based on a **truncated normal distribution**
- **Gradually decrease** the upper bound

## Module #3 Substitute Model Selection

### Generate the candidate adversarial example in the union of adversarial regions of all substitute models

<img src="module3.png" align=right width=575>

- An effective adversarial example can **easily fall in the union** of the adversarial region
- **One substitute model** is selected to generate a candidate adversarial example
- Use **Rosette Wheel Selection** to choose one model with the knowledge of the historical queries

## Module #4 Querying the Target Model

<style scoped>
    h3 {
        margin-top: 0;
        margin-bottom: 0;
    }
</style>

### Update the parameters based on the attack results to limit the sampling region

<center>
    <img src="module4.png" width=700>
</center>

- Update the upper bound
- Update the probability of substitute model selection

## Experiment Setup
<style scoped>
    h3 {
        margin-top: 0;
        margin-bottom: 0;
    }
</style>

### Dataset and Model
![](datasetandmodel.png)

### Evaluation Metrics
![](evaluationmetrics.png)

## Closed-set Evaluation
<style scoped>
    p {
        font-size: 0.9rem;
    }
</style>

Evaluation on ImageNet, where the target and substitute models are trained on the same dataset.

<center>
    <img src="closedsetevaluation.png" width=1080>
</center>

DSA achieves the **highest attack success rate** and **lowest average number of queries**.

## Closed-set Evaluation
<style scoped>
    p {
        font-size: 0.9rem;
    }
</style>

Evaluation on ImageNet, where the target and substitute models are trained on the same dataset.

<center>
    <img src="closedsetevaluation2.png" width=960>
</center>

DSA always achieves the **lowest distance under all test cases** under the same number of queries.

## Closed-set Evaluation
<style scoped>
    p {
        font-size: 0.9rem;
    }
</style>

Evaluate attack performance against ResNet50 integrated with 6 defenses on CIFAR-10.

<center>
    <img src="closedsetevaluation3.png" width=1060>
</center>

DSA is **robust in attacking the defended target models** and achieves the highest ASR with the lowest AvgQ in most tests.

## Open-set Evaluation
Assemble two mutually exclusive datasets for training the substitute and target models.

<center><img src="opensetevaluation.png" width=1150></center>

DSA **maintains high effectiveness and query-efficiency** in the open-set scenario.

## Evaluation on Commercial APIs
<style scoped>
    p {
        font-size: 0.9rem;
    }

    small {
        font-size: 0.8rem;
    }
</style>

Carry out attacks on four well-known commercial DNN services: AWS, Azure, Baidu and Tencent.

<small>
Randomly select 20 images from ImageNet and set the query budget to 1,000.
</small>

<center>
    <img src="evaluationoncommercialapis.png" width=1080>
</center>

DSA can achieve **about 90% ASR within 200 queries** across four commercial APIs.

## Evaluation on Commercial APIs: Case Study
Illustration of adversarial examples on commercial APIs.

![](casestudy.png)

Adversarial examples generated by DSA are mostly approximate to the original images.

## Conclusion

### Contributions
- Propose that **varying the distribution or magnitude of perturbations enables different transferability**
- Propose **an effective and query-efficient adversarial attack** called DSA
- Conduct **comprehensive experiments** to demonstrate the superiority of DSA

### Future Work
- Improve the effectiveness of the attack under $\mathcal l_\infty$ and other norms
- Develop a lightweight and flexible defense to adapt to evolving attack strategies

## Thank you!

Presenter: Li Zhengao (李政翱)