# My Approach and Achievements

## Leveraging Image Channels and False Color

I tackled the challenge of detecting contrails using an innovative strategy. The provided images were captured in 8 different wavelengths, and annotators used a false color image for labeling contrails. I decided to train my model using this false color representation, as it seemed to improve the visibility of contrails and enhance their detectability.

## Effective Interpolation for Boundary Annotations

A significant hurdle I encountered was accurately labeling contrail boundaries. Since contrails suddenly appear or enter from the sides of the image, capturing their true extent was crucial. To address this, I employed an interpolation technique. I first upscaled the image to 384 x 384 and then downscaled it to 256 x 256 using bilinear interpolation. This innovative approach proved successful in obtaining precise boundary labels.

## Iterative Model Refinement

I pursued an iterative model refinement strategy that gradually improved my results. I initially trained a U-Net model from scratch using PyTorch. While this yielded a decent dice score of around `0.54530`, I knew I could do better. I then turned to the `segmentation_models_pytorch` library and selected a U-Net architecture with a pretrained `resnet26` encoder. This choice pushed my dice score up to `0.60388` on the Kaggle Private leaderboard, showcasing noticeable progress. My determination to enhance performance led me to the final step: training a U-Net model with a pretrained `resnet50` encoder using pytorch_lightning library to use multiple Gpus. This decision paid off with a remarkable dice score of `0.64985` on the Kaggle Public leaderboard and `0.64455` on the Private leaderboard ranking me to 346th place.

## Harnessing PyTorch Lightning for Efficiency

To accelerate my training process and streamline metric tracking and hyperparameter management, I adopted the PyTorch Lightning framework. This choice enabled me to harness the power of multiple GPUs efficiently and focus more on experimenting with various strategies rather than dealing with technical intricacies.

# Things that Didn't Work

## Time Dependency and LSTMConv Layer

Understanding that contrails must either suddenly appear or be visible in at least 2 images, I attempted to incorporate the temporal aspect by using an LSTMConv layer in my model. Unfortunately, this approach didn't yield the anticipated results. It appears that the temporal aspect might not play a significant role in this problem, or perhaps the architecture I selected wasn't suited for effectively utilizing this information.

## Exploring Additional Convolutional Layer

In an effort to enhance the model's input capabilities, I added an extra convolutional layer before the input entered the encoder. This adjustment was aimed at allowing the pretrained model to take a 3-channel input while utilizing data from all 8 wavelengths. However, this change didn't lead to significant performance improvements. It appeared that the resnet50 encoder could already effectively extract relevant features from the multi-channel data. As a result, further alterations at this stage in the architecture didn't seem to provide the anticipated benefits.

# Opportunities for Improvement

## Soft Labels for More Accurate Annotations

In hindsight, I realized that using the mean of the annotations from the four annotators might have provided a more accurate representation of contrail regions. This could have potentially reduced noise caused by differing interpretations and improved overall model performance.

## Hybrid Loss Functions

Initially starting with Binary Cross-Entropy and then shifting to Dice Loss to align with evaluation metrics, I now recognize the value of potentially combining both loss functions. This hybrid approach could strike a better balance between precision and recall during training, leading to improved overall results.

My journey in tackling this complex contrail detection problem has taught me a great deal about `model architecture`, `data preprocessing`, and the importance of learning from both successes and failures. Armed with these insights, I am excited to continue refining my strategies and exploring new avenues for even better performance.