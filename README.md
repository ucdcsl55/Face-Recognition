# Face-Recognition

This project is to define and train a DCGAN on a dataset of faces. THe goal is to get a generator network to generate new images of faces that look as realistic as possible!

The project will be broken down into a series of tasks from loading in data to defining and training adversarial networks. At the end of the notebook, I should be able to visualize the results of your trained Generator to see how it performs; generated samples should look like fairly realistic faces with small amounts of noise.

### Learnings throughout the model development process:

1. Generator conv_dim vs. Discriminator conv_dim. At one stage, I was using a similar conv dimension to both my generator and discriminator networks. Unfortunately, the result did not turn out well. Fake images look blurry, unreal and some are scary too. The discriminator's doing fine but don't seem to see any further improvement in the generator network as epoch increases. As a result of that, I have started to tweak my dimensions in this manner. g_conv_dim > d_conv_dim.

2. Latent vector size, z. As part of the learning, we have been taught to use 100. But also taken some initiatives to increase z to gauge any effect to my output. Didnt see any vast improvement on this either.

3. g_loss and d_loss moved up and down randomly and abruptly, moved in opposite direction or simply not converging as expected. It took me a long processing time to understand every single part of the building process and tweak where applicable/possible.

4. Early stopping when the losses are generated in unexpected way. I feel the model has ruined and start to suspect some of the hyperparameters might not be perfectly set. After some rounds of killing the training process, I decided to keep my epochs consistently at 20 for almost every rounds of training. Giving the model some time to train.

5. As for the real and fake loss functions, we were advised to use either cross entropy or a least squares error loss. In this case, I am applying a binary cross entropy with logit loss (BCEWithLogitsLoss) - it combines a sigmoid layer and the BCELoss in one single class. Also, I added labels smoothing just so that I can discourage the discriminator from being overly confident.

6. Mode collapse & learning rate. In the first few initial rounds of training the model, I came to situation where my generator is generating a single image for every possible latent vector that fed through. I believed this is mode collapsing. I sought help through researching some papers and one suggestion is by lowering the learning rate. Learning rate is one of the most important hyperparameters in GAN. During the researching process, I also came across Two Time-Scale Update Rule (TTUR) which presummably can improve my results. As a result of that, am choosing different learning rates for the generator and discriminator networks.

7. Self Attention GAN (SAGAN). It is shown that spectral normalization, a particular kind of normalization applied on the convolutional kernels, can greatly help the stability of the training, especially overcoming vanishing gradient. Accordingly, it was first used only in the discriminator, but later it showed to be effective if also used in the convolutional layers of the generator. So, in my model, I do see vanishing gradient issue after 2-3 rounds of attempts. Hence I have applied this for both networks.


### Suggestion to improve GAN model:

1. Sort "celebrity" faces by race or by ethnicity and batch up separately for training purpose. The concept is similar to sorting cat and dog images from a household pets dataset. This way, the model can learn the precise features faster and better;

2. Have images to include chins and full hair type/style. Unfortunately, these info isn't fully capture in the dataset;

3. Increase image size to 64 / 128 (higher resolution) to improve image quality (e.g. clear face edges vs. background);

4. From model optimisation strategy perspective,
- **Approaches that I applied and added into my network(s) as part of learnings:**
  - Added **self-attention** layers. I used spectral normalisation. This has created some stability in my networks.
  - Added **labels smoothing** so that I can discourage discriminator network to be overconfident. Alternatively, we can use a one-sided label smoothing.
  - Added **flipped labels** to real and fake loss functions. Yes, I did even try setting real loss = 0 and fake loss = 1. Sounds silly.....but the underlying reason is because I do noticed as the epoch increases, my discriminator loss was converging to zero but the generator started to diverge. So, I wanted to improve my generator network and tested by flipping the labels.
  - Applied two different learning rates for different network. Attracted by the concept after reading some research papers on **Two Time-Scale Update Rule (TTUR)**.
  - **Avoid stopping the iteration process early** as networks need time to learn and improve. I kept most of my learning results at least epochs=20.

- **Approaches I came across via researches and it's worth knowing or exploring if time permits:**
  - Add **more convolutional layers and another fully-connected layer** in the generator network or using **larger kernel size** might improve the result slightly.
  - **ResidualBlock** to overcome vanishing gradient issue, if applicable.
  - **Feature Matching**. The “best” image keeps changing when both networks counteract their opponent. This will continue going on and on where both models do not even converge at the end. This is what we called mode collapse. Perhaps feature matching can address that issue.
  - **Minibatch discrimination** When mode collapses, all images created looks similar. To mitigate the problem, we feed real images and generated images into the discriminator separately in different batches and compute the similarity of the image x with images in the same batch.
    **Source:** [Ways_to_improve_GAN_Performance](https://towardsdatascience.com/gan-ways-to-improve-gan-performance-acf37f9f59b#:~:text=%20GAN%20%E2%80%94%20Ways%20to%20improve%20GAN%20performance,t%20models.%205%20Further%20readings.%20%20More)

