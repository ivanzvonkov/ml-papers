# FreezeOut: Accelerate Training by Progressively Freezing Layers

Source: https://arxiv.org/abs/1706.04983

by Andrew Brock, Theodore Lim, J.M. Ritchie & Nick Weston (Edinburgh, UK - Heriot-Watt University)

Year: 2017

## Main Idea

Improve deep learning model training time by progressively decreasing the learning rates of each layer to 0 starting with the first layer and ending with the last layer.

## Intuition

In deep architectures, the early layers learn simple configurations (ie. edge detectors) yet take a large portion of the training budget. The FreezeOut technique stems from the idea that the early layers do not require as much fine tuning as the later layers.

## Math

Each layer's learning rate a<sub>i</sub> depends on

-   the current iteration t and
-   the specific layer's last iteration t<sub>i</sub>

> In all example graphs:
>
> -   y-axis represents percentage of learning rate ( a<sub>i</sub>( t ) )
> -   x-axis represents percentage of iterations ( t )

#### 1. Standard Decrease

a<sub>i</sub>(t) = 0.5 \* a<sub>i</sub>(0)(1 + cos( π t / t<sub>i</sub> ))

<img src='assets/StandardLRDecrease.png'>

#### 2. Scaling decrease by layer's last iteration ( t<sub>i</sub> )

-   Each learning rate travels the same distance

a<sub>i</sub>(t) = 0.5 \* ( a / t<sub>i</sub> )(1 + cos( π t / t<sub>i</sub> ) )

<img src='assets/ScaledLRDecrease.png'>

#### 3. Cubing layer end iteration ( t<sub>i</sub><sup>3</sup> )

-   Gives more time for training later layers

a<sub>i</sub>(t) = 0.5 • a<sub>i</sub>(0)(1 + cos( π t / t<sub>i</sub><sup>3</sup> ))

<img src='assets/StandardLRDecreaseCubedIteration.png'>

#### 4. Scaling decrease and cubing layer end iteration (t<sub>i</sub>):

a<sub>i</sub>(t) = 0.5 • ( a / t<sub>i</sub><sup>3</sup> )(1 + cos( π t / t<sub>i</sub><sup>3</sup> ))

<img src='assets/ScaledLRDecreaseCubedIteration.png'>

## Experiments

Tested each of the four learning rate scheduling strategies on CUFAR-10 and CIFAR-100 with:

-   DenseNets (faster and same accuracy),
-   wide ResNets (faster and better accuracy),
-   VGG (no improvement)

## Conclusion:

Calculation for figuring out how much faster you can train with FreezeOut:

c<sub>i</sub> := cost of one forward or backward pass for a conv layer

C := total cost of training for n iterations

C<sub>without FreezeOut</sub> = ∑ ( 2c<sub>i</sub> • n<sub>iteration</sub>)

C<sub>with FreezeOut</sub> = ∑ ( ( 1 + t<sub>i</sub> ) • c<sub>i</sub> • n<sub>iteration</sub>)

Then the speedup when using FreezeOut is 1 - C<sub>with FreezeOut</sub>/C<sub>without FreezeOut</sub>

Authors recommend a default stratey of cubic scheduling with learning rate scaling with t<sub>0</sub> = 0.8 to minimize training time within a 3% relative error.

## Code

https://github.com/ajbrock/FreezeOut
