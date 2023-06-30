# Python Vfrcompiler Unit test

## OverView
The feature is the implementation of unit test for the python vfrcompiler tool.
## Introduction
We choose ten VFR files from the following modules in the edkrepo as test cases.
- IScsiDxe
- VlanConfigDxe
- Platform
- BootMaintenanceManagerUiLib
- BootManagerUiLib
- DeviceManagerUiLib
- FileExplorerLib
- Ip4Dxe
- UiApp
- RamDiskDxeBoot

The main test function is in **edk2\BaseTools\Source\Python\tests\vfr_yaml_compiler\test_Vfrcompiler.py**

We do the testing from the following two aspects.

1. Function test
- We add function test for the  **PreProcess()** / **Compile()** / **GenBinaryFiles()** / **DumpSourceYaml()** functions of  the tool.
2. Output test
- We add the **test_vfr_lst_file ()** function to do the comparsions.
- To differentiate from the original C tool output files, the python tool generated files are named with prefix 'PyVfr'.
- We compare the python tool generated IFR file with the C tool generated ones to valid the effectiveness of the new tool.

## Add test cases
If you want to add more test cases to do further validation, please do the following steps.
- Open **edk2\BaseTools\Source\Python\tests\pytest_vfrcompiler.ini**
- Add the module you want to test in `target_test_folders`
  
  ```
  target_test_folders =
     Module Name you want to test
  ```

## Run test
To apply the unit test feature, please do the following steps.
1. Add Env: run `pip install pytest` based on the original build environment.
2. run `cd edk2\BaseTools\Source\Python\tests`
3. open **edk2\BaseTools\Source\Python\tests\pytest.ini** and Modify the parameters that need to be used
```
python_files =
    test_Vfrcompiler.py
This parameter selects the test file.
```

4. run command `pytest` in edk2\BaseTools\Source\Python\tests
