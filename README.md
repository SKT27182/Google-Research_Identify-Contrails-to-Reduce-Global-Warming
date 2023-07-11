# Google-Research: Identify Contrails to Reduce Global Warming

Train ML models to identify contrails in satellite images and help prevent their formation

## Goal of the Competition

Contrails are clouds of ice crystals that form in aircraft engine exhaust. They can contribute to global warming by trapping heat in the atmosphere. Researchers have developed models to predict when contrails will form and how much warming they will cause. However, they need to validate these models with satellite imagery.
Your work will help researchers improve the accuracy of their contrail models. This will help airlines avoid creating contrails and reduce their impact on climate change.

## Context

Contrail avoidance is potentially one of the most scalable, cost-effective sustainability solutions available to airlines today. Contrails, short for ‘condensation trails’, are line-shaped clouds of ice crystals that form in aircraft engine exhaust, and are created by airplanes flying through super humid regions in the atmosphere. Persistent contrails contribute as much to global warming as the fuel they burn for flights. This phenomenon was first figured out 75 years ago by military airplanes with the goal to avoid leaving a visible trail. About 30 years ago, climate scientists in Europe began to understand that the contrails blocked heat that normally is released from the earth overnight. They have built powerful models, based on weather data, to identify when contrails will form and how warming they will be. Their research has been validated by other labs as well and it is now well accepted that contrails contribute approximately 1% of all human caused global warming. The motivation behind the use of satellite imagery is to empirically confirm the predictions from these models. With reliable verification, pilots can have confidence in the models and the airline industry can have a trusted way to measure successful contrail avoidance.

<img style="float:center" src="https://storage.googleapis.com/kaggle-media/competitions/Google-Contrails/waterdroplets.png">

Image courtesy of Imperial College

Your work will quantifiably improve the confidence in prediction of contrail forming regions and the techniques to avoid creating them.
Google Research applies ML to opportunities to mitigate climate change and adapt to the changes we already see. We have run research projects in fusion energy plasma modeling, wildfire early detection, optimal car routing, and forecasts for climate disasters. 

## EValuation

This competition is evaluated on the global [Dice coefficient](https://en.wikipedia.org/wiki/Sørensen–Dice_coefficient). The Dice coefficient can be used to compare the pixel-wise agreement between a predicted segmentation and its corresponding ground truth. The formula is given by:

$$ Dice = \frac{2*|X \cap Y|}{|X| + |Y|} $$

where X is the entire set of predicted contrail pixels for all observations in the test data and Y is the ground truth set of all contrail pixels in the test data.


## Data Description

In this competition you will be using geostationary satellite images to identify aviation contrails. The original satellite images were obtained from the [GOES-16 Advanced Baseline Imager (ABI)](https://www.goes-r.gov/spacesegment/abi.html), which is publicly available on [Google Cloud Storage](https://console.cloud.google.com/storage/browser/gcp-public-data-goes-16/). The original full-disk images were reprojected using bilinear resampling to generate a local scene image. Because contrails are easier to identify with temporal context, a sequence of images at 10-minute intervals are provided. Each example (record_id) contains exactly one labeled frame.

Learn more about the dataset from the preprint: [OpenContrails: Benchmarking Contrail Detection on GOES-16 ABI](https://arxiv.org/abs/2304.02122). Labeling instructions can be found at in this [supplementary material](https://storage.googleapis.com/goes_contrails_dataset/20230419/Contrail_Detection_Dataset_Instruction.pdf). Some key labeling guidance:

- Contrails must contain at least 10 pixels
- At some time in their life, Contrails must be at least 3x longer than they are wide
- Contrails must either appear suddenly or enter from the sides of the image
- Contrails should be visible in at least two image

Ground truth was determined by (generally) 4+ different labelers annotating each image. Pixels were considered a contrail when >50% of the labelers annotated it as such. Individual annotations (human_individual_masks.npy) as well as the aggregated ground truth annotations (human_pixel_masks.npy) are included in the training data. The validation data only includes the aggregated ground truth annotations.

This is an example of labeled contrails. Code to produce images like this can be found in this [notebook](https://www.kaggle.com/code/inversion/visualizing-contrails).


<img alt="" src="https://www.googleapis.com/download/storage/v1/b/kaggle-user-content/o/inbox%2F59561%2F590a0bc76044a4ceb71368cf3b62412e%2Fcontrails_600.png?generation=1683669162469942&amp;alt=media">

### Files


- **train/** - the training set; each folder represents a record_id and contains the following data:
    - **band_{08-16}.npy**: array with size of $H * W * T$, where $T = n_{times\_before} + n_{times\_after} + 1$, representing the number of images in the sequence. There are $n_{times\_before}$ and $n_{times\_after}$ images before and after the labeled frame respectively. In our dataset all examples have $n_{times\_before}=4$ and $n_{times_after}=3$. Each band represents an infrared channel at different wavelengths and is converted to brightness temperatures based on the calibration parameters. The number in the filename corresponds to the GOES-16 ABI band number. Details of the ABI bands can be found [here](https://www.goes-r.gov/mission/ABI-bands-quick-info.html).

    - **human_individual_masks.npy**: array with size of $H * W * 1 * R$. Each example is labeled by R individual human labelers. R is not the same for all samples. The labeled masks have value either 0 or 1 and correspond to the ($n_{times\_before}$+1)-th image in band_{08-16}.npy. They are available only in the training set.

    - **human_pixel_masks.npy**: array with size of $H * W * 1$ containing the binary ground truth. A pixel is regarded as contrail pixel in evaluation if it is labeled as contrail by more than half of the labelers. 


- **validation/** - the same as the training set, without the individual label annotations; it is permitted to use this as training data if desired


- **test/** - the test set; your objective is to identify contrails found in these records. **Note**: Since this is a Code competition, you do not have access to the actual test set that your notebook is rerun against. The records shown here are copies of the first two records of the validation data (without the labels). The hidden test set is approximately the same size (± 5%) as the validation set. **IMPORTANT**: Submissions should use run-length encoding with empty predictions (e.g., no contrails) should be marked by '-' in the submission. (See this [notebook](https://www.kaggle.com/code/inversion/contrails-rle-submission) for details.)

- **{train|validation}_metadata.json** - metadata information for each record; contains the timestamps and the projection parameters to reproduce the satellite images.

- **sample_submission.csv** - a sample submission file in the correct format





## Submission

In order to reduce the submission file size, our metric uses run-length encoding on the pixel values.  Instead of submitting an exhaustive list of indices for your segmentation, you will submit pairs of values that contain a start position and a run length, e.g., '1 3' implies starting at pixel 1 and running a total of 3 pixels (1,2,3).

Note that, at the time of encoding, the mask should be binary, meaning the masks for all objects in an image are joined into a single large mask. A value of 0 should indicate pixels that are not masked, and a value of 1 will indicate pixels that are masked.

The competition format requires a space delimited list of pairs. For example, '1 3 10 5' implies pixels 1,2,3,10,11,12,13,14 are to be included in the mask. The metric checks that the pairs are sorted, positive, and the decoded pixel values are not duplicated. The pixels are numbered from top to bottom, then left to right: 1 is pixel (1,1), 2 is pixel (2,1), etc.

## Acknowledgments

MIT Laboratory for Aviation and the Environment led by MIT Professor Steven Barrett
Satellite images are from NOAA GOES-16, see more in https://www.goes-r.gov/.