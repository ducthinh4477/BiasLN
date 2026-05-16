# BiasLN DeepfakeBench

This repository is a cleaned DeepfakeBench workspace focused on the team's
main detector, **BiasLN**.

The old auxiliary detector implementations and detector configs have been
removed. The shared project pipeline is still kept:

- dataset loading and preprocessing
- training and testing entrypoints
- trainer, metrics, logging, and optimizers
- BiasLN detector registration

## Model

BiasLN is registered under:

```text
model_name: biasln
```

Main files:

- `training/detectors/biasln_detector.py`
- `training/config/detector/biasln.yaml`

BiasLN uses CLIP ViT-L/14 vision features, freezes the backbone by default,
and trains the classifier head plus LayerNorm and bias parameters.

## Train

```bash
python training/train.py \
  --detector_path ./training/config/detector/biasln.yaml \
  --train_dataset "FaceForensics++" \
  --test_dataset "Celeb-DF-v2"
```

The same command is also available in `train.sh`.

## Test

```bash
python training/test.py \
  --detector_path ./training/config/detector/biasln.yaml \
  --test_dataset "Celeb-DF-v2" \
  --weights_path /path/to/biasln_weights.pth
```

The same command template is also available in `test.sh` and
`training/test.sh`.

## Data

The project still follows the DeepfakeBench data layout. Configure dataset
paths in:

- `training/config/train_config.yaml`
- `training/config/test_config.yaml`
- `preprocessing/config.yaml`

If your data has not been rearranged into JSON metadata yet, run:

```bash
cd preprocessing
python rearrange.py
```

For raw video preprocessing, configure `preprocessing/config.yaml`, place the
dlib landmark model under `preprocessing/dlib_tools`, then run:

```bash
cd preprocessing
python preprocess.py
```

## Notes

Only BiasLN detector code is kept in `training/detectors`, and only
`biasln.yaml` is kept in `training/config/detector`. If you add a new model in
the future, register it in `training/detectors/__init__.py` and provide its
detector config under `training/config/detector`.
