##HR Analytics Dashboard – Power BI Portfolio Project
##Executive Summary

This comprehensive Power BI dashboard delivers actionable workforce insights for Synergy Group's leadership and HR team. The solution provides real-time monitoring of workforce dynamics, hiring trends, diversity metrics, employee performance, and attrition analysis to support strategic decision-making.

Project Objectives

The dashboard enables stakeholders to:

Monitor workforce size and organizational structure

Understand hiring trends and recruitment patterns

Track diversity and inclusion metrics

Analyze attrition patterns and identify key drivers

Dashboard Architecture
Report Pages

Overview – High-level workforce metrics and KPIs

Demographics – Detailed diversity and demographic analysis

Data Model Design

The solution implements a star schema architecture with the following components:

Dimension Tables

DimEmployee – Employee master data

DimDate – Calendar dimension for time intelligence

DimEducationLevel – Education classification lookup

DimSatisfiedLevel – Satisfaction rating scale

DimRatingLevel – Performance rating scale

Fact Table

FactPerformanceRating – Employee performance reviews and ratings

Implementation Guide
1. Data Loading & Preparation

All datasets were imported as CSV files from a local directory:

Source File	Target Table
Employee.csv	DimEmployee
EducationLevel.csv	DimEducationLevel
PerformanceRating.csv	FactPerformanceRating
RatingLevel.csv	DimRatingLevel
SatisfiedLevel.csv	DimSatisfiedLevel
