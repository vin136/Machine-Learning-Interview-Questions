How to handle lack of labels ?

1. Weak supervision : heuristic rules to label. eg: NLP sentiment : `if get rich fast` => spam. Encoding  domain expertise. Even if giving for hand-labeler, better to 


# Training-data generation with GAN's

Problem: CT SCANS segmentation WITH SOME Contrast and non-contrast images.

Here since the ground truth labeling remains same for both the Constrast and non-contrast images. Also found it to work well on out-of-sample : say scans from different hospital.

Used CycleGAN : conditional GAN.

Active learning approaches:

1. 100 train samples with labels, rest 900 unlabeled. Train on 100 samples and based on confidence of the predictions in the rest, decide to label them. This assumes the model is well-caliberated.


