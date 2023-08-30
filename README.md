# :microscope: Advanced Testing Workshop :microscope:
Welcome to the Advanced Testing workshop of Coalesce 2023!

## :lab_coat: About this Project :test_tube:
You've been using dbt for a while, and are comfortable with the out-of-the-box dbt tests:
- unique
- not_null
- accepted_values
- relationships

But... a part of you wonders if there's other tests you should be using. What are some best practices?
dbt Community Slack... documentation... on-demand courses... blog posts... there's so many places and 
such little time!

Well, if you've decided to join us for this workshop - you've come to the right place.  
We'll hold hands and walk through advanced testing concepts together. By the end of it you'll be an 
expert at all the tools at your disposal and can rest easy knowing how to improve your data quality checks.

## :white_check_mark: Prerequisites:  
This workshop assumes that you're familiar with dbt.  

At a minimum you should know how to:
- Apply and run out-of-the-box dbt tests
- Execute dbt commmands
- Use git functionality
- Write basic SQL

It's a bonus if:
- You understand languages like Jinja (templating) or Python (scripting) 
- You've written some basic Jinja or Python statements in the past

<details>
  <summary> Live Participant Instructions </summary>
  
  For those joining us in the San Diego workshop, you can either bring your own dbt Project or will
  be given access to our sandbox dbt Cloud account with all the necessary prerequisites.

  :toolbox: **Setup**
  
  1. Navigate to the `Coalesce 2022 Workshop - Advanced testing` account.
  2. Configure your development credentials:
     1. Click on your user profile in the top left-hand corner and click `Profile Settings` 
     2. Scroll to the "Credentials" section.
     3. Click on `Analytics`
     4. Hit the `Edit` button in the lower right hand corner.
     5. Change these configurations:
        - schema: Set this to `dbt_` your first initial + last name. Example: `dbt_cberger`
        - target: Set this to `dev`
     6. Hit `Save`
    

  3. Create a new branch named with your first initial + last name, suffixed with `coalesce_23`.  
     Example: `cohms_coalesce_23`
  4. Run `dbt deps` to install dependencies.

  5. Confirm your setup:  
     1. Navigate to the IDE by clicking on the `Develop` tab in the upper right-hand corner
     2. Try running the following commands:
        ```bash
        $ dbt run
        $ dbt test
        ```
        or alternatively:
        ```bash
        $ dbt build
        ```
        **Don't worry when you see an error on `stg_tpch__part_suppliers`. When you hit this error you're ready to start the lesson!**

</details>

<details>
  <summary> Completing the Workshop by Following Along or Asyncronously </summary>

  What you need:

  1. *A Repository*   
     Ideally, with the files and folders contained in this workshop. To make a copy,
     [fork this repository](https://docs.github.com/en/get-started/quickstart/fork-a-repo).
  2. *dbt*  
     Using dbt Cloud vs. dbt Core doesn't matter. You'll specifically want to know how to:
     - [install packages](https://docs.getdbt.com/docs/building-a-dbt-project/package-management)
     - [use built-in tests](https://docs.getdbt.com/docs/building-a-dbt-project/tests)
     - [use selection syntax](https://docs.getdbt.com/reference/node-selection/syntax)
     - [upgrade your dbt version](https://docs.getdbt.com/guides/migration/versions/upgrading-to-v1.3), if needed (This project uses v1.3)

     To setup dbt:
     - [dbt Cloud Setup](https://docs.getdbt.com/guides/getting-started)
     - [dbt Core Setup](https://docs.getdbt.com/guides/getting-started/learning-more/getting-started-dbt-core)
  3. *Some Data*  
     This project is written on top of [Snowflake](https://signup.snowflake.com/)
     and uses the publicly available [TPC-H data set](https://docs.snowflake.com/en/user-guide/sample-data-tpch) 
     which is be included in a Snowflake trial account.

     A truncated version of the data set has also been included in this project as CSV files if you're using a
     different data platform - this is located in the [resources](/_resources/tpch_dataset/) folder.
      
     If you don't have some data or a data platform yet, don't worry - these guides are great for 
     getting you set up:
     **BigQuery**
     - [Instructions for setting up a free BigQuery account](https://docs.getdbt.com/guides/getting-started/getting-set-up/setting-up-bigquery)  
     - [Instructions for loading CSV files into BigQuery](https://cloud.google.com/bigquery/docs/samples/bigquery-load-table-gcs-csv)  
     - [dbt Cloud quickstart for BigQuery](https://docs.getdbt.com/quickstarts/bigquery?step=1)
     - [Starter instructions](https://relational.fit.cvut.cz/dataset/TPCH) for accessing the TPC-H dataset yourself

     **Snowflake**
     - [Instructions for setting up a free Snowflake account](https://docs.snowflake.com/en/user-guide/admin-trial-account#signing-up-for-a-trial-account)
     - [Instructions for viewing the TPCH dataset](https://docs.snowflake.com/en/user-guide/sample-data-using#viewing-the-sample-database)
     - [dbt Cloud quickstart for Snowflake](https://docs.getdbt.com/quickstarts/snowflake?step=1)
   
     **Important**: We don't suggest seeding the CSV files. Though they are truncated, there are still
     a signifcant amount of rows. As a best practice we use seed functionality for small and static datasets
     where seeds are more performant for this specific purpose.

  :toolbox: **Setup**

  1. [Fork this repository](https://docs.github.com/en/get-started/quickstart/fork-a-repo).
  2. Set up your dbt Project    
      - [dbt Cloud Setup](https://docs.getdbt.com/guides/getting-started)
      - [dbt Core Setup](https://docs.getdbt.com/guides/getting-started/learning-more/getting-started-dbt-core)
  3. Load the data, if you don't have it already  
     Truncated TPCH data is located in [`_resources/tpch_dataset`](/_resources/tpch_dataset/).
  
     If you set up a BigQuery account during setup, you can load the data following these steps:
     1. In the BigQuery UI's `Explorer` pane, click the three dots next to your project name 
     2. Click `Create dataset`.
     3. For `Dataset ID`, type `raw_tpch`.
     4. Click `Create dataset`
     5. You should now see your dataset listed under your project name. Click the three dots next to the dataset.
     6. Click `Create table`
     7. Choose `Upload` as the **Create table from** option.
     8. Click `Browse` under `Select file` 
     9. Upload each file you downloaded from the [`_resources/tpch_dataset`](/_resources/tpch_dataset/) folder:  
        - For the **table name**, use the file name without the extension. Some file names have `_100mb` appended. Omit this.
        - Make sure to check `Auto detect` under **Schema** 
     
     **Troubleshooting**:  
     If you have the data loaded but are getting errors in the dbt project, you will need to 
     change the contents of the `models/staging/tpch/_sources.yml` file.

  4. Run `dbt deps` to install dependencies.
  5. Confirm your setup:  
     Try running the following commands:
     ```bash
     $ dbt run
     $ dbt test
     ```
     or alternatively:
     ```bash
     $ dbt build
     ```
  
     **Don't worry when you see an error on `stg_tpch__part_suppliers`. When you hit this error you're ready to start the lesson!**
  
</details>

## :tada: If you've made it this far, congratulations! :tada:
You're ready to start the workshop!

**Live participants:**  
We'll be training together live :purple_heart: - we appreciate the go-getters, but if you want
to do the exercises with us in the workshop, you can stop here!

## Additional Helpful Links:
- Learn more about dbt [in the docs](https://docs.getdbt.com/docs/introduction)
- Check out [Discourse](https://discourse.getdbt.com/) for commonly asked questions and answers
- Join the [dbt community](http://community.getbdt.com/) to learn from other analytics engineers
- Find [dbt events](https://events.getdbt.com) near you
- Check out [the blog](https://blog.getdbt.com/) for the latest news on dbt's development and best practices
