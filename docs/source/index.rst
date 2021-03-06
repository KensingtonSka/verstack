##################
verstack 0.3.1 Documentation
##################
Machine learning tools to make a Data Scientist's work efficient

veratack package contains the following tools:

- **NaNImputer** impute all missing values in a pandas dataframe using advanced machine learning with 1 line of code
- **stratified_continuous_split** create train/test splits stratified on the continuous variable


.. note:: 

  Getting verstack

  $ ``pip install verstack``

  $ ``pip install --upgrade verstack``


******************
NaNImputer
******************

Initialize NaNImputer
===========================
.. code-block:: python

  from verstack import NaNImputer
  
  # initialize with default parameters
  imputer = NaNImputer()
  
  # initialize with selected parameters
  imputer = NaNImputer(conservative = False, 
                       n_feats = 10, 
                       nan_cols = None, 
                       fix_string_nans = True, 
                       multiprocessing_load = 3, 
                       verbose = True, 
                       fill_nans_in_pure_text = True, 
                       drop_empty_cols = True, drop_nan_cols_with_constant = True)

Parameters
===========================
* ``conservative`` [default=False]

  - Model complexity level used to impute missing values. If ``True``: model will be set to less complex and much faster.

* ``n_feats`` [default=10]

  - Number of corellated independent features to be used forcorresponding column (with NaN) model training and imputation.

* ``nan_cols`` [default=None]

  - List of columns to impute missing values in. If None: all the columns with missing values will be used.


* ``fix_string_nans`` [default=True]

  - Find possible missing values in numeric columns that had been (mistakenly) encoded as strings, E.g. 'Missing'/'NaN'/'No data' and replace them with np.nan for further imputation.

* ``multiprocessing_load`` [default=3]

  - Levels of parallel multiprocessing compute
    - 1 = single core
    - 2 = half of all available cores
    - 3 = all available cores

* ``verbose`` [default=True]

  - Print the imputation progress.

* ``fill_nans_in_pure_text`` [default=True]

  - Fill the missing values in text fields by string 'Missing_data'.Applicable for text fields (not categoric).

* ``drop_empty_cols`` [default=True]

  - Drop columns with all NaNs.

* ``drop_nan_cols_with_constant`` [default=True]

  - Drop columns containing NaNs and known values as a single constant.

* ``feature_selection`` [default="correlation"]
  - Define algorithm to select most important feats for each column imputation. Quick option: "correlation" is based on selecting n_feats with the highest binary correlation with each column for NaNs imputation. Less quick but more precise: "feature_importance" is based on extracting feature_importances from an xgboost model.


Examples
================================================================

Using NaNImputer with all default parameters

.. code-block:: python

  imputer = NaNImputer()
  df_imputed = imputer.impute(df)

Say you would like to impute missing values in a list of specific columns, use 20 most important features for each of these columns imputation and deploy a half of the available cpu cores

.. code-block:: python

  imputer = NaNImputer(nan_cols = ['col1', 'col2'], n_feats = 20, multiprocessing_load = 2)
  df_imputed = imputer.impute(df)

Logic
================================================================

With NaNImputer you can fill missing values in numeric, binary and categoric columns in your pandas dataframe using advanced XGBRegressor/XGBClassifier models with just 1 line of code. Regardless of the data types in your dataframe (string/bool/numeric): 

 - all of the columns will be checked for missing values
 - transformed into numeric formats
 - split into subsets with and without missing values
 - applicalbe models will be selected and configured for each of the columns with NaNs
 - models will be trained in multiprocessing mode utilizing all the available cores and threads of your cpu (this saves a lot of time)
 - NaNs will be predicted and placed into corresponding indixes
 - columns with all NaNs will be droped
 - columns containing NaNs and known values as a single constant
 - data will be reverse-transformed into original format

The module is highly configurable with default argumets set for the highest performance and verbosity

The only limitation is:

- NaNs in pure text columns are not imputed. By default they are filled with 'Missing_data' value. Configurable. If disabled - will return these columns with missing values untouched

******************
stratified_continuous_split
******************

Create stratified splits based on either continuous or categoric target variable.
  - For continuous target variable verstack uses binning and categoric split based on bins
  - For categoric target enhanced sklearn.model_selection.train_test_split is used: in case there are not enough categories for the split, the minority classes will be combined with nearest neighbors.

Can accept only pandas.DataFrame/pandas.Series as data input.

.. code-block:: python 

  verstack.stratified_continuous_split.scsplit(*args, 
                                               stratify, 
                                               test_size = 0.3, 
                                               train_size = 0.7, 
                                               continuous = True, 
                                               random_state = None)

Parameters
===========================
* ``X,y,data`` 

  - data input for the split in pandas.DataFrame/pandas.Series format.

* ``stratify`` 

  - target variable for the split in pandas/eries format.

* ``test_size`` [default=0.3]

  - test split ratio.

* ``train_size`` [default=0.7]

  - train split ratio.

* ``continuous`` [default=True]

  - stratification target definition. If True, verstack will perform the stratification on the continuous target variable, if False, sklearn.model_selection.train_test_split will be performed with verstack enhancements.

* ``random_state`` [default=5]

  - random state value.


Examples
================================================================

.. code-block:: python

  from verstack.stratified_continuous_split import scsplit
  
  train, test = scsplit(data, stratify = data['continuous_column_name'])
  X_train, X_val, y_train, y_val = scsplit(X, y, stratify = y, 
                                           test_size = 0.3, random_state = 5)


******************
Links
******************
`Git <https://github.com/DanilZherebtsov/verstack>`_

`pypi <https://pypi.org/project/verstack/>`_

`author <https://www.linkedin.com/in/danil-zherebtsov/>`_