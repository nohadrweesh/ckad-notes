## Labels & Selectors & Annotations
- Labels: group things together based on specific criteria
- Selectors: filter things based on specific criteria
- Annotations: record informatory data about the objects and added to metadata like build -version, contact details
- k get pods --selector env=prod
- k get pods -l env=prod