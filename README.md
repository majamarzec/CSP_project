# CSP_project
A project for EEG Laboratory classes 2025
Author: Maja Marzec


This project comprises 3 steps:

1. data assesment
2. ERDS & CSP analysis
3. ML classifier


## data assesment

Action points realized in "data_assesment" notebook:
- eeg signal upload from raw (bin), xlm, csv and tag files -> eeg dictionary with the option to call Fs, channel_names, rescaled values, tags
- filtering [lowpass - 45Hz, highpass - 3Hz, notch - 50Hz]
- applying chosen montage; options: common_average, linked ears, chosen reference channel
- signal cutting for particular task describtion -> dictionary and signal save to local files

Action points realized in "data_assesment miu" notebook:
- eeg signal upload from raw (bin), xlm, csv and tag files -> eeg dictionary with the option to call Fs, channel_names, rescaled values, tags
- filtering [lowpass - 13Hz, highpass - 8Hz, notch - 50Hz] -- filtering to leave only the miu band
- applying chosen montage; options: common_average, linked ears, chosen reference channel
- signal cutting for particular task describtion -> dictionary and signal save to local files


Task describtion: "ruch" (move) or "wyobrazenie" (imaginary move) 

## ERDS and CSP analysis

It is presented for 3 approaches. 

### A. "erds ruch" or "erds wyobrazenie"

0. Upload signal and dictionary prepared in "data_assesment"
1. computation of spectrogram with noverlap = 1
2. visualization of spectrogram averaged over trials (for each hand: left or right, color scale: global or local)

The interesting part in ERDS analysis is relative power to baseline. 
Used 2 paradigms: classical ERDS & statistical

b - baseline
f - chosen freq
t - chosen time

#1: S = (P(t,f) - mean(P(b,f)))/ mean(P(b,f)))
#2: S = (miu(t,f) - miu(b,f))/std(b)

3. computation of relative power for both paradigms
4. visualization of relative power averaged over trials (for both paradigms: statistical and classic ERDS, for each hand: left or right, color scale - global)

5. CSP:
  - Extracts movement-phase EEG data (t > 2s) and baseline data (t < 2s) from trials.
  - Computes trial-wise covariance matrices separately for each class (left, right).
  - Averages covariance matrices across trials for stable estimation.
  - Solves a generalized eigenvalue problem R_L * w =λ* R_P * w using function from scipy.linalg library -> Lambda, W = sl.eigh(R_L, R_P)
  - W matrix examination in search for maximizing filter
  - topology-oriented look and see how the chosen vector W (we have 19 vectors) looks on the head topology (interesting topologies - P and C)
  - When computing a spe ctrogram on a signal retrieved from CSP, noverlap = 10 because the initial time resolution will produce too much points for classifictaion task
  - CSP components computation and visualization (for each hand: left or right, color scale: global or local)
  - choice of "the best" filter + data for classification acquistion, labelling and dataset local saving in folder "datasets"

### B. "erds ruch miu" or "erds wyobrazenie miu"

0. Upload signal and dictionary prepared in "data_assesment miu"
1-5 all the same

This approach was to test the hypothesis that additional preprocessing of signal in miu band will positively affect classification performance

### C . "erds ruch 2" or "erds wyobrazenie 2" ( + ML classifier)

We want to split EEG_signal by trials for train & test datasets 
Why? To make sure that there is no data leakage through "W" matrix
We'll calculate the W only from traintest and apply to both train and test datasets.

0. Upload signal and dictionary prepared in "data_assesment"
1. train_test split
2. CSP algorythm on ONLY TRAIN signals
3. CSP components calculation and vizualization for both train and test splits to make sure they "match"
4. choice of "the best" filter + data for classification acquistion, labelling
5. classification using xgboost model & Logistic Regression (5 fold CV + ROC curve + metrics)

## ML classifier (for approaches that may cause data leakage)

5-fold cross-validation
Tested models: xgboost, Logistic Regression, RF, SVC

