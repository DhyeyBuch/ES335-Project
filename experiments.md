# A running log for all the tasks done in the project so far...

## **Dataset 1. The NYC Yellow Taxi Trip Dataset**
- Finalized dataset #1 for the project; it has good scope for showing the importance of feature engineering. Various useful features can be engineered from the given limited set of features.
- Early thoughts - No random train/val/test split; we will have to split based on time to avoid leakage.
- Also, over time, the underlying distribution may change, which violates the assumption of supervised learning... A chronological split will expose our model to that more honestly.

### **Scope for Preprocessing and Feature Engineering**
- The feature `Extra` includes the $0.50 and $1 rush hour and overnight charges; we could derive a useful feature like rush hour from here.
- Could use the drop-off latitude and longitude to classify busy regions
- We could extract various useful features from Date and time - the hour of day could be segregated into 3-4 slots - peak hours, lazy hours, etc. 
