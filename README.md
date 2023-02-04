

<!-- actdocs start -->

## Description

Make matrix values for next jobs.

matrix_value_from_file_action action is designed to make matrix value(eg. `"["test1", "test2"]"`)
Using matrix makes us to handle loop procedures.
This action will run following steps:

0. (caller's step) Write multiline values to a plain text file.
1. Convert the plain text file to a json file.
2. Convert the json file to a compacted array using jq and stored in the output.

## Inputs

| Name | Description | Default | Required |
| :--- | :---------- | :------ | :------: |
| file_path | file path | n/a | yes |

## Outputs

| Name | Description |
| :--- | :---------- |
| result | matrix value result |

<!-- actdocs end -->

## Usage

  ```yaml
  jobs:
    prepare_matrix_value:
      outputs:
        result: ${{ steps.make_matrix_value.outputs.result }} # Define outputs to next jobs.
      steps:
        - name: make multiline values named tests # Make multiline files for loop procedure.
          run: |
            cat << EOF > test_data
            test-1
            test-2
            test-3
            EOF
        - name: make matrix value # Store matrix value to `steps.<step_name>.<id>.result`.
          id: make_matrix_value
          uses: yuki0920/matrix-value-fromfile-action@v0
          with:
            file_path: test_data
    use_matrix_value:
      strategy:
        matrix:
          value: ${{ fromJSON(needs.prepare_matrix_value.outputs.result) }} # Use matrix value.
      steps:
        - name: echo value # Replase with some loop procedures.
          run: |
            echo "${{ matrix.value }}"
  ```
