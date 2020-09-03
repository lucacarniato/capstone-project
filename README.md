# The capstone project

Machine learning model for predicting severity from other features



df['STATUS'] = df['STATUS'].astype("|S")
df['ADDRTYPE'] = df['ADDRTYPE'].astype('str')
df['LOCATION'] = df['LOCATION'].astype('str')
df['JUNCTIONTYPE'] = df['JUNCTIONTYPE'].astype('str')
df['SDOT_COLDESC'] = df['SDOT_COLDESC'].astype('str')
df['UNDERINFL'] = df['UNDERINFL'].astype('str')
df['WEATHER'] = df['WEATHER'].astype('str')
df['ROADCOND'] = df['ROADCOND'].astype('str')
df['LIGHTCOND'] = df['LIGHTCOND'].astype('str')
df['HITPARKEDCAR'] = df['HITPARKEDCAR'].astype('str')


oe = OrdinalEncoder()
X_train_fs = X_train
X_train_fs['SEVERITYCODE'] = y_train
X_train_fs = X_train.dropna()
oe.fit(X_train_fs)
X_train_fs_enc = oe.transform(X_train_fs)
X_train_fs_enc