# brit-code-validation
Assessment for Brit Assessment validation

1. Understanding the API Documentation : 

The API under test is https://restful-api.dev 
It provides CRUD endpoints to create, read, update, and delete objects.

Test Cases for PATCH /objects/{id}


Test Case                                           	      Description	                                                 Expected Result

Testcase-01 – Update a field successfully	          Update price of an existing object id.	                             Response code 200, updated field reflects new value, 
                                                                                                                          updated At timestamp present.
Testcase-02 – Add a new field                       Add a new field like color to an existing object.                    Response contains new field with correct value.
 
Testcase -03 – Invalid object id                    Try to update a non-existent id                                      Response code 404 with error message.

Testcase 04 – Invalid request body                   Send malformed JSON.                                                 Response code 400 with error message.

Testcase 05 – No change request                   Send empty payload {}.                                                  Response code 200, no changes applied.


file : test_patch_api.py

import requests
import pytest

BASE_URL = "https://api.restful-api.dev/objects"

@pytest.mark.parametrize("object_id", ["6"])  
def test_patch_update_price(object_id):
    //Testcase -01  – Update existing field (price)
    payload = {"data": {"price": 150}}
    response = requests.patch(f"{BASE_URL}/{object_id}", json=payload)
    assert response.status_code == 200
    body = response.json()
    assert body["data"]["price"] == 150
    assert "updatedAt" in body

@pytest.mark.parametrize("object_id", ["6"])
def test_patch_add_color(object_id):
    //Testcase 02– Add a new field (color)//
    payload = {"data": {"color": "black"}}
    response = requests.patch(f"{BASE_URL}/{object_id}", json=payload)
    assert response.status_code == 200
    body = response.json()
    assert body["data"]["color"] == "black"

def test_patch_invalid_object():
    //Testcase -03  – Invalid id should return 404//
    payload = {"data": {"price": 999}}
    response = requests.patch(f"{BASE_URL}/999999", json=payload)
    assert response.status_code == 404

def test_patch_invalid_body():
    //testcase -04  – Invalid JSON body should return 400//
    headers = {"Content-Type": "application/json"}
    # pushing the  bad payload
    bad_payload = "{'data': 'bad-json'}"
    response = requests.patch(f"{BASE_URL}/6", data=bad_payload, headers=headers)
    assert response.status_code in [400, 415]  # Response based on Server ip requests 

def test_patch_empty_payload():
    //testcase -05 " Empty payload should not break API" // 
    response = requests.patch(f"{BASE_URL}/6", json={})
    assert response.status_code == 200




CI/CD Pipeline integration using yaml 

name: API Tests

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

jobs:
  test:
    runs-on: ubuntu-test_image

    steps:
    - name: Checkout brit_repo
      uses: actions/checkout@v3

    - name: Set up Python
      uses: actions/setup-python@v4
      with:
        python-version: "3.10"

    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install requests pytest

    - name: Run tests
      run: pytest -v --runmode=2 



git clone : https://github.com/bpkuldeeppatil-AutomationQA-Code/brit-code-validation

cd brit-code-validation

run 

pytest test_patch_api.py


    

                                                                                                                          
                                                                                                                    
                                                                                                                         
                                                                                                                         
                                                                                                                         
