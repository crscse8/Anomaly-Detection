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
* At every time slice, I calculate the cross correlation matrix (CCM) for all signals on some predetermined window of time slices
* I flatten the CCM and append it to the signal data at the end of each window
* I then mold the data to make it into a suitable input for an LSTM, establishing some predetermined input time sequence. Thus in this model, there are two separate time windows: (1) the cross-correlation performed on a previous window of time slices (initially 20) and (2) the LSTM input time sequence if time slices (initially 10). 
* I then feed the data into an LSTM with 4 nodes

## Status
* This is a work in progress. There is a whole host of possible tuning experiments we could attempt with this model:
  (1) Changing the size of the cross-correlation window
  (2) Changing the size of the LSTM input sequence window
  (3) Changing the sampling rate to make the windows extend over a larger segment of the signal without increasing computational overhead
  (3) LSTM tuning, for example:
      * Varying numbers of nodes
      * Varying numbers of epochs
      * Stacking LSTMs
  (4) Acquiring more complete data. What we have now might be unbalanced because most of it as labeled Attack=True, meaning jamming is present.    
* **Results**: So far, the ROC-AUC results are atrocious. I suspect the windows are not covering large enough segments of the signals, but increasing the size of the cross correlation window is very computationally expensive. I have a machine doing the cross correlation with a window of 100 right now. So far it has run 20 hours and still has not completed.

## Conclusion from Early Results
* Initial results with Hao's Random Forest using *all* the parameters *except* AGC & CN0 were much better than my results using an LSTM whose *only* inputs are AGC and CN0.
* But before discarding the signal processing + LSTM approach, we should:
  * Explore using LSTMs over larger windows and different more sparse sampling rates
  * Try the model with different data. In current data, Attack = True dominates considerably. 
* There may be ways to enhanced the sophistication of the signal analysis, such as singular signal analysis (SSA)
