# Sleep Score Formula

\begin{align}
D &= \text{clip}\left(100 - 15\left|H - T(A)\right|,\ 0,\ 100\right) \\[6pt]
r &= \left(\frac{H - 0.25}{1.5}\right) \bmod 1 \\[6pt]
C &= \text{clip}\left(100 - 80\min(r,\ 1-r),\ 0,\ 100\right) \\[6pt]
Q &= \text{clip}\left(10 \cdot Q_{1\text{-}10},\ 0,\ 100\right) \\[6pt]
R &= \text{clip}\left(100 - 2\max\left(0,\ \left|HR - E(A)\right| - 5\right),\ 0,\ 100\right) \\[6pt]
P_{\text{disorder}} &=
\begin{cases}
20 & \text{if sleep apnea} \\
10 & \text{if insomnia} \\
0 & \text{otherwise}
\end{cases} \\[6pt]
P_{\text{stress}} &= 2 \cdot S_{1\text{-}10} \\[10pt]
\text{SleepScore} &= \text{clip}\left(
\frac{0.3\left(\dfrac{D + C}{2}\right) + 0.3\,Q + 0.2\,R - P_{\text{disorder}} - P_{\text{stress}}}{0.8},\ 0,\ 100
\right)
\end{align}




**where:**

- $H$ = hours slept
- $A$ = age
- $T(A)$ = age-adjusted target sleep hours
- $Q_{1\text{-}10}$ = self-reported sleep quality rating
- $HR$ = resting heart rate (bpm)
- $E(A)$ = age-adjusted expected resting heart rate
- $S_{1\text{-}10}$ = self-reported stress level

!mkdir -p ~/.kaggle
!cp kaggle.json ~/.kaggle/
!chmod 600 ~/.kaggle/kaggle.json

!kaggle datasets download -d uom190346a/sleep-health-and-lifestyle-dataset

!unzip -o sleep-health-and-lifestyle-dataset.zip -d sleep_data

import pandas as pd
import numpy as np
df = pd.read_csv("sleep_data/Sleep_health_and_lifestyle_dataset.csv")
print(f"shape of original dataframe: {df.shape}")
print(f"columns of original dataframe: {df.columns}")

df.head()

def target_sleep_hours(age):
  """
  This function takes age(float) as an input and returns the ideal number of hours of sleep
  Reference :   https://www.sleepfoundation.org/sleep-calculator#sleep-cycle-calculator--how-to-determine-how-much-sleep-you-need

  Parameters:
    age (float): age in years
  Returns:
    duration (float): target sleep duration in hours
  """
  if age < 1:
    return 14.0
  elif age < 3:
    return 12.5
  elif age < 6:
    return 11.5
  elif age < 13:
    return 10.5
  elif age < 18:
    return 9.0
  else :
    return 8.0

def cycle_alignment_score(sleep_duration_hours, cycle_length=1.5, fall_asleep_hours=0.25):
  """
    This function calculates the quality of sleep by considering awakenings mid cycle
    Parameters:
      sleep_duration_hours (float): total amount of time in bed
      sleep_duration_hours (float): length of each cycle in hours
      fall_asleep_hours (float): total amount of time taken to fall asleep considering at 0.25 hours
    Returns:

  """
  time_asleep = max(0.0,sleep_duration_hours-fall_asleep_hours)
  cycles = time_asleep / cycle_length
  reminder = cycles - np.floor(cycles)
  distance_from_whole_cycle = min(reminder,1-reminder)
  score = 100 -80 * distance_from_whole_cycle
  return float(np.clip(score,0,100))


def expected_heartrate_resting(age):
  """
  This function gives the expected_heartrate_resting given the age
  """
  if age < 30:
    return 65.0
  elif age < 50:
    return 68.0
  elif age < 65:
    return 70.0
  else:
    return 72.0

---

Component Scores

def duration_score(hours, age):
  """
  This function takes in hours of sleep and the age of a person and returns the duration score
  """
  target = target_sleep_hours(age)
  score =  100 - 15 * abs(hours-target)
  return float(np.clip(score,0,100))

def quality_score(quality_1_to_10):
  """
    Rescales the dataset's subjective 1-10 quality rating to 0-100.
  """
  return float(np.clip(quality_1_to_10*10,0,100))

def heart_rate_score(resting_hr, age):
  """
  This function calculates a score on the basis of resting heart rate of person by age.
  """
  expected = expected_heartrate_resting(age)
  deviation = max(0.0, abs(resting_hr - expected) - 5)
  score = 100 - 2 * deviation
  return float(np.clip(score,0,100))

def disorder_penalty(disorder):
  if not isinstance(disorder, str):
    return 0.0
  d = disorder.strip().lower()
  if d == "sleep apnea":
    return 20.0
  if d == "insomnia":
    return 10.0
  return 0.0

def stress_penalty(stress_level_1_to_10):
  return 2.0 * stress_level_1_to_10

Computing actual sleep score

def compute_sleep_score(row):
  d = duration_score(row["Sleep Duration"], row["Age"])
  c = cycle_alignment_score(row["Sleep Duration"])
  duration_cycle = 0.5*d + 0.5*c

  q = quality_score(row["Quality of Sleep"])
  h = heart_rate_score(row["Heart Rate"], row["Age"])
  penalty = disorder_penalty(row.get("Sleep Disorder")) + stress_penalty(row["Stress Level"])

  raw = 0.3 * duration_cycle + 0.3 * q + 0.2 * h - penalty
  score = raw/0.8
  return float(np.clip(score,0,100))

df["sleep_score"] = df.apply(compute_sleep_score, axis=1)

Sleep Score for the first row of the dataset

row = df.loc[0]
score_0 = compute_sleep_score(row)
print(score_0)

def sleep_class(sleep_score):
  """
  This function takes sleep_score as an input and outputs a class of Sleep

  Parameters:
    sleep_score(int)
  Output:
    sleep_class(str)
  """
  if sleep_score <= 40:
    return "Very Low"
  elif sleep_score <= 60:
    return "Low"
  elif sleep_score <= 80:
    return "OK"
  elif sleep_score <= 95:
    return "High"
  else:
    return "Very High"

sleep_class(score_0)

### Calculating Mental Health Score from Sleep Score and Stress

def mental_health_score(sleep_score, stress_level_1_to_10):
  """
  This function takes sleep score and stress levels as input and computes the
  mental health of a person

  Parameters:
    sleep_score(float): sleep score of a person
    stress_level_1_to_10(int): stress levels of a person

  Returns:
    mental_health_score(float): Estimate of mental health of a person from  sleep score and stress levels

  """
  stress = 100 - (stress_level_1_to_10*10)
  score = 0.6 * sleep_score + 0.4 * stress
  return float(np.clip(score,0,100))

def mental_health_class(mental_health_score):
  """
  This function takes mental_health_score as an input and outputs a
  mental health risk category.
  """
  if mental_health_score <= 40:
    return "At Risk"
  elif mental_health_score <= 60:
    return "Struggling"
  elif mental_health_score <= 80:
    return "OK"
  elif mental_health_score <= 95:
    return "Good"
  else:
    return "Thriving"

row = df.iloc[0]
s_score = compute_sleep_score(row)
mh_score = mental_health_score(s_score, row["Stress Level"])
mh_class = mental_health_class(mh_score)
print(s_score, mh_score, mh_class)

df["sleep_score"] = df.apply(compute_sleep_score, axis=1)
df["mental_health_score"] = df.apply(lambda row: mental_health_score(row["sleep_score"], row["Stress Level"]), axis=1)
df["mental_health_class"] = df["mental_health_score"].apply(mental_health_class)

from sklearn.linear_model import LinearRegression
from sklearn.model_selection import train_test_split
from sklearn.metrics import mean_squared_error, r2_score

def train_mental_health_regressor(df):
  """
  Trains a linear regression model to predict mental_health_score from
  sleep_score and supporting features

  Parameters:
    df (DataFrame): must already have 'sleep_score' and 'mental_health_score' columns
  Returns:
    model (LinearRegression): the fitted model
  """
  features = df[["sleep_score", "Age", "Sleep Duration", "Heart Rate"]].copy()
  features["has_apnea"] = (df["Sleep Disorder"].str.lower() == "sleep apnea").astype(int)
  features["has_insomnia"] = (df["Sleep Disorder"].str.lower() == "insomnia").astype(int)

  y = df["mental_health_score"]

  X_train, X_test, y_train, y_test = train_test_split(
      features, y, test_size=0.25, random_state=42
  )

  model = LinearRegression()
  model.fit(X_train, y_train)

  preds = model.predict(X_test)

  print("--- Mental Health Score Prediction (Linear Regression) ---")
  print(f"R^2 score: {r2_score(y_test, preds):.3f}")
  print(f"MSE: {mean_squared_error(y_test, preds):.3f}")

  coefs = pd.Series(model.coef_, index=features.columns).sort_values(key=abs, ascending=False)
  print("\nFeature coefficients:")
  print(coefs.to_string())
  print(f"\nIntercept: {model.intercept_:.3f}")

  return model

df["sleep_score"] = df.apply(compute_sleep_score, axis=1)
df["mental_health_score"] = df.apply(lambda row: mental_health_score(row["sleep_score"], row["Stress Level"]), axis=1)

model = train_mental_health_regressor(df)

## Refernces

> Dataset: Sleep Health and Lifestyle Dataset. Kaggle. Retrieved from https://www.kaggle.com/datasets/uom190346a/sleep-health-and-lifestyle-dataset

1. https://www.sleepfoundation.org/sleep-calculator - for specific age  brackets and 90 min cycles
