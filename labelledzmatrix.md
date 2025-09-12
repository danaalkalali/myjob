## _This is a simple MATLAB script to label your CONN toolbox Z matrix with the ROI names on the x and y axes._
#### 1. Load your .mat file
```
data = load('/Users/dna8993/Downloads/resultsROI_Subject002_Condition001-2.mat');
```

#### 2. Get Z matrix
```
Z = data.Z;
```

#### 3. Get ROI names from 'names' or 'names2'
```
if isfield(data, 'names')
    roi_names = data.names;
elseif isfield(data, 'names2')
    roi_names = data.names2;
else
    error('ROI names not found');
end
```
#### 4. Flatten if nested
```
if iscell(roi_names) && iscell(roi_names{1})
    roi_names = roi_names{1};
end
```

#### 5. Clean ROI names for table variable names
```
clean_names = matlab.lang.makeValidName(roi_names);
```
#### 6. Create table with clean names as VariableNames, original as RowNames
```
T = array2table(Z, 'VariableNames', clean_names, 'RowNames', roi_names);
```

#### Save to Excel
```
writetable(T, '/Users/dna8993/Downloads/ROI_connectivity_matrix.xlsx', 'WriteRowNames', true);
disp('Excel file saved as ROI_connectivity_matrix.xlsx, yay');
```
