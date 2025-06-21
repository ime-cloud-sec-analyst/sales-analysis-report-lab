Sales Analysis Report Lab

Overview

This repository contains all artifacts and documentation for the AWS Serverless Sales Analysis Report lab. In this lab, you build a serverless solution that:

Extracts sales data from a MySQL database hosted on an EC2 LAMP instance using a Lambda function in a VPC.

Formats and publishes a report via SNS to email subscribers on a defined schedule.

Architecture

EventBridge (8 PM Mon–Sat, UTC)
        ↓
salesAnalysisReport Lambda (Python 3.9)
  • Retrieves DB credentials from SSM Parameter Store
  • Invokes salesAnalysisReportDataExtractor
        ↓
salesAnalysisReportDataExtractor Lambda (Python 3.9 + PyMySQL layer)
  • Connects to MySQL on EC2 (Cafe VPC)
  • Runs analytical query on cafe_db
        ↓
salesAnalysisReport Lambda
  • Formats JSON into a text report
  • Publishes to SNS topic (salesAnalysisReportTopic)
        ↓
SNS → Email subscription → Administrator inbox

Repository Structure

/images         # 47 screenshots from the lab
README.md       # This document

Prerequisites

AWS account with permissions to create Lambda, IAM, SNS, Parameter Store, EventBridge, and EC2 resources.

AWS CLI or Console access configured for the us-west-2 region.

EC2 Instance running MySQL (CafeInstance) and populated with sample café data.

Lab-provided ZIP files:

pymysql-v3.zip

salesAnalysisReportDataExtractor-v3.zip

salesAnalysisReport-v2.zip

Lab Tasks Summary

IAM Role Setup

Inspect salesAnalysisReportRole and salesAnalysisReportDERole in IAM.

Lambda Layer

Create pymysqlLibrary layer from pymysql-v3.zip.

Data Extractor Lambda

Author salesAnalysisReportDataExtractor function using salesAnalysisReportDataExtractor-v3.zip.

Attach PyMySQL layer, configure VPC/subnet/security group.

Test and troubleshoot timeout and network issues.

Populate & Test Data

Place orders on the café website to generate sample data.

Re-run extractor to verify JSON output.

SNS Configuration

Create salesAnalysisReportTopic, subscribe email endpoint, confirm subscription.

Main Lambda Deployment

Create salesAnalysisReport function (via AWS CLI or Console) with salesAnalysisReport-v2.zip.

Set topicARN environment variable to the SNS topic ARN.

Unit test for status code 200 and email delivery.

Scheduling

Add EventBridge rule salesAnalysisReportDailyTrigger with cron expression cron(0 20 ? * MON-SAT *) for 8 PM UTC Mon–Sat.

Verify automated daily report emails.

Usage

Manual Test: Use the Lambda console's Test tab to invoke functions.

Scheduled Run: Rely on EventBridge to invoke salesAnalysisReport daily.

Review Reports: Check subscribed email inbox for the daily sales report.

Cleanup

To avoid AWS charges, delete the following resources when finished:

Lambda functions: salesAnalysisReport, salesAnalysisReportDataExtractor

SNS topic: salesAnalysisReportTopic

EventBridge rule: salesAnalysisReportDailyTrigger

IAM roles and policies created for the lab.

License

This lab documentation is released under the MIT License. Feel free to reuse and adapt.

