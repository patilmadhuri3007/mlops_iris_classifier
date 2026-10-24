Verification & Execution Steps
•	pytest -v runs all test files under tests/ and reports each test individually as PASSED. Expected terminal excerpt:
tests/test_preprocess.py::test_preprocess_removes_duplicates PASSED
tests/test_preprocess.py::test_preprocess_imputes_missing_numeric_values PASSED
tests/test_features.py::test_sepal_and_petal_area_computed_correctly PASSED
tests/test_validate.py::test_validate_fails_on_out_of_range_value PASSED
tests/test_training_pipeline.py::test_model_predict_proba_sums_to_one PASSED
tests/test_model_quality.py::test_model_meets_minimum_accuracy_threshold PASSED
======================== 17 passed in 4.82s ========================
•	pytest --cov=src --cov-report=term-missing produces a coverage table; src/pipeline/preprocess.py, features.py, and validate.py should each show coverage above 85%, confirming the unit tests exercise the majority of branches (including the edge cases from Step 2-4).
•	test-results.xml is generated in JUnit XML format — open it and confirm it contains <testsuite> and <testcase> elements for every test, since this is the format CI tools (Experiment 9) parse to display pass/fail summaries.
•	To confirm the quality gate test actually catches regressions: temporarily lower MIN_ACCEPTABLE_ACCURACY to 0.99 and re-run pytest tests/test_model_quality.py — it should now FAIL, demonstrating the test correctly blocks an unrealistic/regressed threshold; restore it to 0.90 afterward.
Viva-Voce / Post-Lab Questions
Q1. Why do ML model tests typically assert on properties (e.g., “accuracy exceeds 0.90,” “probabilities sum to 1”) rather than exact expected output values, unlike most traditional software unit tests? Answer: Traditional software is generally deterministic — a given input always maps to exactly the same output, so asserting exact equality is both possible and meaningful. ML models, however, can legitimately produce slightly different numeric outputs across runs due to sources of randomness such as random weight initialization, data shuffling order, or non-deterministic parallel/GPU floating-point operations — meaning exact-value assertions would be brittle and could fail intermittently even when the model is functioning correctly. Property-based assertions (predictions fall within the valid label set, probabilities are well-formed, accuracy exceeds a meaningful threshold) instead verify the invariants that must hold for the model to be considered correct and usable, remaining robust to legitimate run-to-run numeric variation while still reliably catching genuine bugs or quality regressions.
