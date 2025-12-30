## 05/18/2024:
SCIN DATASET
READ THESE FOR POSSIBLE MODELS TO USE
https://huggingface.co/Anwarkh1/Skin_Cancer-Image_Classification
https://huggingface.co/Muzmmillcoste/finetuned-dermnet/blob/main/README.md
https://www.kaggle.com/code/mishki/resnet-keras-code-from-scratch-train-on-gpu

Model must use only up-close images for its training.

Research whether it is possible to detect up-close images and classify condition labels in 1 model.

## 05/19/2024
Realized that weights are not particularly relevant for the [[SCIN Dataset]], so the model will just predict the dermatologist assigned labels regardless of weight.
However, I will try to experiment regarding a model's ability to predict multiple labels AND their corresponding likelihood.