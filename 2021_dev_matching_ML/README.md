# Image Classification

### Hyper-parameter

Seed: 0

epochs: 100

early stop count: 10

Learning rate: 1e-3

batch size: 32

num workers: 4

### Architecture

Model: swsl_resnext50_32x4d (pretrained)

Optimizer: AdamW

Loss function: LabelSmoothingLoss(), smoothing=0.5

Scheduler: ReduceLROnPlateau(T_max=3)