# Machine Learning

---

## Amazon SageMaker

A fully managed service for developers and data scientists to build, train, and deploy ML models at scale. SageMaker simplifies the ML workflow by providing a unified interface and automating infrastructure provisioning.

### Typical ML Workflow

1. **Data Collection:** Gather and label historical data.
2. **Data Preparation:** Clean, transform, and split data into training and test sets.
3. **Model Training:** Use the training dataset to build the ML model.
4. **Model Evaluation:** Apply the model to test data to evaluate performance.
5. **Deployment:** Deploy the model for predictions, classifications, clustering, etc.

---

## Feature Store

- A **feature** is a property or attribute used to train an ML model.
- Feature Store provides fast, secure, and organized access to feature data for training and inference.
- Enables sharing and reusing features across different models and teams.
- **Security:** Data is encrypted at rest and in transit (KMS integration), fine-grained IAM access control, and can be secured via PrivateLink.

---

## ML Lineage Tracking

Lineage tracking creates and stores ML workflow metadata (MLOps), maintaining a running history of ML models for audit and compliance. Entities can be auto-tracked or manually created. AWS Resource Access Manager (RAM) can be integrated for cross-account lineage.

### Lineage Tracking Entities

- **Trial Component:** A processing job, training job, or transform job.
- **Trial:** Composed of trial components.
- **Experiment:** A group of trials for a given use case.
- **Context:** Logical grouping of entities.
- **Action:** A workflow step (e.g., model deployment).
- **Artifact:** An object or data used in an action.
- **Association:** Connects entities together through an association type.

- Users can query lineage entities using the `LineageQueryAPI` from Python and visualize them with the external visualizer helper class.

---

## SageMaker Data Wrangler

A visual interface to prepare data for ML. Data Wrangler allows you to import, visualize, and transform data, and quickly prototype models or prepare data for full-scale ML training.

**Typical Data Wrangler Workflow:**

1. **Data Source:** Connect to data sources (S3, Redshift, Athena, etc.).
2. **Import Data:** Load data into Data Wrangler.
3. **Preview Data:** Inspect and understand the data.
4. **Visualize Data:** Explore distributions, correlations, and outliers.
5. **Transform Data:** Clean, normalize, and engineer features.
6. **Quick Model:** Instantly train a simple model for rapid evaluation.
7. **Export Data Flow:** Export the transformation pipeline for use in SageMaker pipelines or jobs.

**Core Troubleshooting Tips:**

- Ensure correct IAM permissions for Data Wrangler and data sources.
- Verify data sources allow access from Data Wrangler.
- Check that the EC2 instance limits are sufficient for the demands of the SageMaker instance or ML model.

---