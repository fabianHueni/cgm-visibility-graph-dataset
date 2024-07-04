# Continous Glucose Monitoring Data as Visibility Graphs

This repository contains visibility graph files generated from continuous glucose monitoring (CGM) data of 37 patients
in a resolution of 5 minutes, specifically for the purpose of hypoglycemia prediction using Graph Neural Networks (
GNNs).
The data origins from the work of Garcia-Tirado *et al.* [1].
The CGM data is transformed into graphs using the concept of natural visibility graphs [2], and the resulting files are
organized by window size, prediction horizon, and patient ID.

## Key Concepts

- **Window Size**: The number of time stamps considered for each prediction. This repository contains files for window
  sizes of 6, 12, and 24 time stamps.
- **Prediction Horizon**: The number of time stamps into the future for which a prediction is made. This repository
  includes prediction horizons of 15, 30, and 60 minutes.
- **Visibility Graph**: A method to transform time series data into a graph, where each time stamp is a node, and edges
  are created based on the visibility between time stamps.

## Labeling Process

The labeling process is crucial for identifying hypoglycemia events within the CGM data. The labels are generated based
on the prediction horizon and the blood glucose level threshold.

**Threshold for Hypoglycemia**: A blood glucose level below 70 mg/dL is considered a hypoglycemia event.

**Label Generation**:
- If any glucose measurement within the prediction horizon falls below 70 mg/dL, the sample is
labeled as a hypoglycemia event (positive label).
- If none of the measurements within the prediction horizon fall below the threshold, the sample is labeled as
non-hypoglycemia (negative label).

This approach ensures that the labels reflect the presence of hypoglycemia events within the specified prediction
horizon.

## Data Transformation

The time series CGM data is transformed into visibility graphs for each patient, window size, and prediction horizon.
The transformed graphs are stored in TFRecord format, which is efficient for training machine learning models in
TensorFlow.

Each node has two attributes:

- **reading**: The glucose value at that time stamp in mg/dl.
- **order**: The position of the node in the time series data. This enables the models to include the position of the
  node in the sequence.

Each edge has one attribute:

- **distance**: The length of the edge. This corresponds to the euclidean distance between the two nodes the edge is
  connecting.

The visibility graph is annotated with the following attributes:

- **critical**: A binary attribute indicating whether the sample corresponds to a hypoglycemia event.
- **patient_id**: The id of the patient from which this sample originate.
- **true_proportion**: The proportion of samples which corresponds to a hypoglycemia event of the patient. This can be
  used to wight the classes according to the true/false proportion to address the class imbalance.

## Repository Structure

The repository is structured as follows:

```
CGM-Visibility-Graphs/
├── input-6/
│   ├── horizon-15/
│   │   ├── patient-1/
│   │   │   ├── 85101-train.tfrecord
│   │   │   ├── 85101-val.tfrecord
│   │   ├── patient-2/
│   │   │   ├── 85102-train.tfrecord
│   │   │   ├── 85102-val.tfrecord
│   │   └── ...
│   ├── horizon-30/
│   ├── horizon-60/
│   └── ...
├── input-12/
│   ├── horizon-15/
│   ├── horizon-30/
│   ├── horizon-60/
│   └── ...
├── input-24/
│   ├── horizon-15/
│   ├── horizon-30/
│   ├── horizon-60/
│   └── ...
└── README.md
```

### Folder Descriptions

- **input-6/**: Contains visibility graph files for a window size of 6 time stamps (30 minutes).
- **input-12/**: Contains visibility graph files for a window size of 12 time stamps (60 minutes).
- **input-24/**: Contains visibility graph files for a window size of 24 time stamps (120 minutes).

Each of these folders is further divided into subfolders based on the prediction horizon:

- **horizon-15/**: Prediction horizon of 15 minutes.
- **horizon-30/**: Prediction horizon of 30 minutes.
- **horizon-60/**: Prediction horizon of 60 minutes.

Within each prediction horizon folder, there are subfolders for each patient, identified by their patient ID. Each
patient folder contains:

- **<patient-id>-train.tfrecord**: Training data (70%) in TFRecord format.
- **<patient-id>-val.tfrecord**: Validation data (30%) in TFRecord format.

## Usage

To use the data in this repository for training and evaluating models:

1. **Clone the repository**

2. **Load the TFRecord files**: Use TensorFlow's `tf.data.TFRecordDataset` to load and preprocess the data for your
   models.

3. **Train and evaluate models**: Use the loaded data to train and evaluate models for hypoglycemia prediction.

## Citation

If you use this data in your research, please cite this repository and the original CGM dataset source.

## Acknowledgments

Calculations were performed on UBELIX (https://www.id.unibe.ch/hpc), the HPC cluster at the University of Bern.

## License

This repository is licensed under the GPL-3.0 License. See the [LICENSE](LICENSE) file for more details.

## Bibliography

[1] Jose Garcia-Tirado, Patricio Colmegna, Orianne Villard, Jenny Diaz Castaneda, Rebeca Esquivel-Zuniga, Chaitanya
Koravi, Charlotte Barnett, Mary Oliveri, Morgan Fuller, Sue Brown, Mark Deboer, and Marc Breton. Assessment of meal
anticipation for improving fully automated insulin delivery in adults with type 1 diabetes. Diabetes care, 46, 07 2023.

[2]  Lucas Lacasa, Bartolo Luque, Fernando Ballesteros, Jordi Luque, and Juan Carlos Nu˜no. From time series to complex
networks: The visibility graph. Proceedings of the National Academy of Sciences, 105(13):4972–4975, April 2008.
