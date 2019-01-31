# Anomaly-Detection 

## Thumbnail Description of the Problem

* Jamming can provoke a fall in AGC & CN0 levels as the operational amplifier that regulates signal strength attempts to compensate in the presence of signal power surge
* Thus a drop in AGC or CN0 may signify the presence of jamming 
* However, the AGC in any particular phone can vary for other reasons, such as obstacles that temporarily obstruct the reception of GPS signals
* Thus a more reliable indicator would be the group behavior of clusters of phones 
* clusters of phonesin proximity to one another tend to haveAGC & CN0 levelsthat track one another fairly closely albeit with a lag or phase difference

## Challenges in Using AGC & CN0 as a Metric

How to measure the synchronicity of AGC levels in clusters of phones, given that:
* They may be out of phase
* AGC levels do not have fixed thresholds we can use to measure anomalies, and so we may want to count both small and large drops in AGC provided these changes occur in multiple phones

## Model exploration:

Our team is tackling this problem with two different approaches to see which works best:
(1) Random Forest ML that makes predictions based on all available inputs except AGC & CN0 
(2) A signal analysis approach that feeds cross correlation data into an LSTM 

I was assigned (2).

## Anomaly Detection Using an LSTM Front-Loaded with Cross Correlation Data from AGC & CN0 Signals

* I sorted the data into 106 separate phone signals and serialized it using a "local time stamp" gathered by SOFWERX engineers
* At every time slice, I calculate the cross correlation matrix (CCM) for all signals on a window of 100 time slices
* I flatten the CCM and append it to the signal data at the end of each window
* I then mold the data to make it into a suitable input for an LSTM, establishing a time sequence window of 10 time slices. Thus in this model, there are two separate lookaheads: (1) the cross-correlation that looks ahead by 20 time slices and (2) the LSTM time slice window that looks ahead by 10 time slices. 
* I then feed the data into an LSTM with 4 nodes

## Status
* This is a work in progress. There is a whole host of possible tuning experiments we could attempt with this model:
  (1) Changing the size of the cross-correlation window
  (2) Changing the size of the LSTM input sequence window
  (3) Increasing the number of epochs in the LSTM.
  (4) Exploring different metrics
  (5) Acquiring more complete data. What we have now might be unbalanced because most of it as labeled Attack=True, meaning jamming is present.    
* I have not completed the intial implementation of this, still need to do some basic things like standardize the data. I have no plots to speak of. Accuracy in an initial trial run was suspiciously high.

## Potential Paths of Future Exploration
* As indicated above, a lot of experimentation could be done with the LSTM 
* There may be ways to enhanced the sophistication of the signal analysis, such as singular signal analysis (SSA)
