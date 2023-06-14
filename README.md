# Template: Standard Robot Framework

This is the simplest template to start from.

- Get started from a simple task template in `tasks.robot`.
  - Uses [Robot Framework](https://robocorp.com/docs/languages-and-frameworks/robot-framework/basics) syntax.
- You can configure your robot `robot.yaml`.
- You can configure dependencies in `conda.yaml`.

## Learning materials

- [Robocorp Developer Training Courses](https://robocorp.com/docs/courses)
- [Documentation links on Robot Framework](https://robocorp.com/docs/languages-and-frameworks/robot-framework)
- [Example bots in Robocorp Portal](https://robocorp.com/portal)
# my-rsb-robot


*** Settings ***
Documentation       Inhuman Insurance, Inc. Artificial Intelligence System robot.
Library    RPA.HTTP
Library    RPA.JSON
Library    RPA.Tables
Library    Collections


*** Variables ***
${TRAFFIC_JSON_FILE_PATH}=      ${TRAFFIC_JSON_FILE_PATH}

*** Tasks ***
Produce traffic data work items
    Download traffic data
    ${traffic_data}=    Load traffice data as table
    ${filtered_data}=    Filter and sort traffic data    ${traffic_data}
    ${filtered_data}=    Get latest data by country    ${filtered_data}

*** Keywords ***
Download traffic data
    Download
    ...     https://github.com/robocorp/inhuman-insurance-inc/raw/main/RS_198.json  
    ...      ${OUTPUT_DIR}${/}traffic.json
    ...      overwrite=True

Load traffice data as table
    ${json}=    Load JSON from file    ${TRAFFIC_JSON_FILE_PATH}
    ${table}=   Create Table    ${json}[value]
    RETURN    ${table}

Filter and sort traffic data
    [Arguments]        ${table}
    ${max_rate}=       Set Variable      ${5.0}
    ${rate_key}=       Set Variable      NumericValue
    ${gender_key}=     Set Variable      Dim1
    ${both_genders}=    Set Variable     BTSX
    ${year_key}=        Set Variable     TimeDim
    Filter Table By Column    ${table}    ${rate_key}      <    ${max_rate}
    Filter Table By Column    ${table}    ${gender_key}    ==    ${both_genders}
    Sort Table By Column      ${table}    ${year_key}      False
    RETURN    ${table}

Get latest data by country
    [Arguments]    ${table}
    ${country_key}=    Set Variable    SpatialDim
    ${table}=    Group Table By Column    ${table}    ${country_key}
    ${latest_data_by_country}=    Create List
    FOR    ${group}    IN    @{table}
        ${first_row}=    Pop Table Row    ${group}
        Append To List    ${latest_data_by_country}    ${first_row}
    END
    RETURN    ${latest_data_by_country}
