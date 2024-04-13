# Apache Airflow Plugin for Power BI Dataset Refresh. 🚀

## Introduction
Get ready to enhance your Apache Airflow workflows with a new plugin designed specifically for refreshing Power BI datasets! The plugin contains the custom operator to seamlessly handle dataset refresh and it supports SPN authentication. Additionally, the operator checks for existing refreshes before triggering the new one.

## How to Use
### Getting Started
To utilize this operator, simply fork the repository locally and you're ready to roll.

### Authentication
Before diving in,
* You must have <strong>Admin account of Power BI</strong>.
* The plugin supports the <strong>SPN (Service Principal) authentication</strong> with the Power BI. You need to add your service prinicpal as the <strong>Contributor</strong> in your Power BI workspace.

Since custom connection forms aren't feasible in Apache Airflow plugins, credentials must be stored in the secret backend. Here's what you need to store:
1. `client_id`: The Client ID of your service principal.
2. `client_secret`: The Client Secret of your service principal.
3. `tenant_id`: The Tenant Id of your service principal.

## Features
* #### Xcom Integration: The Power BI Dataset refresh operator enriches the Xcom with essential fields for downstream tasks:
1. `powerbi_dataset_refresh_id`: Request Id of the Dataset Refresh.
2. `powerbi_dataset_refresh_status`: Refresh Status.
    * `Unknown`: Refresh state is unknown or a refresh is in progress.
    * `Completed`: Refresh successfully completed.
    * `Failed`: Refresh failed (details in `powerbi_dataset_refresh_error`).
    * `Disabled`: Refresh is disabled by a selective refresh.
3. `powerbi_dataset_refresh_end_time`: The end date and time of the refresh (may be None if a refresh is in progress)
4. `powerbi_dataset_refresh_error`: Failure error code in JSON format (None if no error)

* #### External Monitoring link: The operator conveniently provides a redirect link to the Power BI UI for monitoring refreshes.

## Sample DAG to use the plugin.

Ready to give it a spin? Check out the sample DAG code below:

```python
from datetime import datetime

from airflow import DAG
from airflow.operators.bash import BashOperator
from operators.powerbi_refresh_dataset_operator import PowerBIDatasetRefreshOperator


with DAG(
        dag_id='refresh_dataset_powerbi',
        schedule_interval=None,
        start_date=datetime(2023, 8, 7),
        catchup=False,
        concurrency=20,
        tags=['powerbi', 'dataset', 'refresh']
) as dag:

    refresh_in_given_workspace = PowerBIDatasetRefreshOperator(
        task_id="refresh_in_given_workspace",
        dataset_id="<dataset_id",
        group_id="workspace_id",
        force_refresh = False,
        wait_for_termination = False
    )

    refresh_in_given_workspace

```

Feel free to tweak and tailor this DAG to suit your needs!

🌟 Please feel free to share any thoughts or suggestions you have.