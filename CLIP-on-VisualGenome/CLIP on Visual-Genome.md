# CLIP on Visual-Genome

In this project, I try to recreate [CLIP (Contrastive Language-Image Pre-training) model by Open - AI](https://openai.com/blog/clip/). 

![CLIP%20on%20Visual-Genome%203268994c5566470ba0dd8461e8de92e0/Untitled.png](CLIP%20on%20Visual-Genome%203268994c5566470ba0dd8461e8de92e0/Untitled.png)

CLIP Description by Open - AI.

### How does CLIP work?

![CLIP%20on%20Visual-Genome%203268994c5566470ba0dd8461e8de92e0/Untitled%201.png](CLIP%20on%20Visual-Genome%203268994c5566470ba0dd8461e8de92e0/Untitled%201.png)

CLIP architecture is a dual encoder architecture pre-trained using contrastive loss. 

A batch of images are given as input to the Image Encoder. Each of these images are converted into embeddings. Lets call these embeddings i1, i2, i3, . . . . iN for now, where n is the number of images in a batch.

A batch of phrases, corresponding to each image, is given as input to the Text Encoder. Each of these are also then converted into embeddings with the same dimensions as the Image embeddings. Lets call these embeddings t1, t2, t3 . . . . tN.

The goal of the encoders is to output embeddings such that the 

- Cosine distance between the Image embedding vector and the Text embedding vector is the close to 0 : when images and text which correspond to each other
- Cosine distance between the Image embedding vector and the Text embedding vector is the close to 1 when they are not related.

During Training, the images and their respective texts occupy the same position in the batch ie, t1 is the phrase that corresponds to i1. Similarly t2 corresponds to i2 and so on. 

Due to this, as show in the image above, dot(i1, t1) has to be close to 1 and all others should be close to 0 in that row. Therefore when you multiply Image embeddings and Text embeddings, ideally you should get a diagonal matrix with diagonal elements being 1. This diagonal matrix is then used to calculate contrastive loss.

It is also due to this reason that contrastive loss gives better performance with bigger batch sizes.

## Implementing CLIP

Instead of scraping data from the internet for images and captions, I used Visual Genome Dataset to pre-train CLIP. 

![CLIP%20on%20Visual-Genome%203268994c5566470ba0dd8461e8de92e0/Untitled%202.png](CLIP%20on%20Visual-Genome%203268994c5566470ba0dd8461e8de92e0/Untitled%202.png)

I used the region descriptions of 80000 images to train the network. 8000 images were used as the validation dataset. 

First I tried combined 2 region descriptions into a sentence to use as text for each image but this problem proved to hard to solve for such a small amount of data. Finally, I used a single phrase to describe each image in a training example. 

I made a data pipeline using **tfrecords**. The Vision Encoder was build using **EfficientNetB5**. The Text Encoder was build using **small-BERT.** 

Optimizer used was AdamW with learning rate = 1e-4 and weight decay = 1e-4. I used a batch size of 128.

## Zero Shot Classification on Pascal VOC

![CLIP%20on%20Visual-Genome%203268994c5566470ba0dd8461e8de92e0/Untitled%203.png](CLIP%20on%20Visual-Genome%203268994c5566470ba0dd8461e8de92e0/Untitled%203.png)

### How To use CLIP for zero shot classification?

1. We first get the all the category/text embeddings using the text encoder. This gives us the array in purple in the image with text embeddings.
2. We take an image from the pascal voc dataset, and we pass it to the vision encoder. This gives us the image embeddings which is in green in the image.
3. We then multiple the two matrixes and we use softmax on its product.
4. All the categories for which the value is close to 1 or above a certain threshold, are the predicted categories. 

This gives us our Multi-Label Zero Shot Classifier. 

### Results For Zero Shot Classification

![CLIP%20on%20Visual-Genome%203268994c5566470ba0dd8461e8de92e0/Untitled%204.png](CLIP%20on%20Visual-Genome%203268994c5566470ba0dd8461e8de92e0/Untitled%204.png)

By Adjusting the threshold, we can easily optimize the model for accuracy, sensitivity or specificity.