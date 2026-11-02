# Email OTP Verification Bypass via Response Manipulation — TaskBear

## Vulnerability Type

Email Verification Bypass

## Severity

Medium

## Summary

Replacing an invalid OTP response (`401 Unauthorized`) with a captured success response (`201 Created`) allowed registration to continue. After submitting the remaining details, the application displayed an account-created notification and opened the dashboard.

## Steps to Reproduce

1. Open TaskBear's sign-up page and enter a controlled test email.
2. Submit a valid OTP and save its `201 Created` response in Burp.
3. Intercept another verification request and change the OTP to `123456`.
4. Forward it and capture the `401 Unauthorized` response: `OTP invalid or expired`.
5. Replace the failure status and body with the saved success response. Forward it.
6. Complete the remaining registration fields and submit the form.
7. Observe the success notification and dashboard.

## Proof of Concept

Screenshots from the supplied class exercise:

### 1. Sign-up page

![TaskBear sign-up form](https://github.com/user-attachments/assets/b4538357-5483-4f1f-8eda-4e0f3ae75e14)

### 2. Valid OTP — success response

![Valid email OTP verification response](https://github.com/user-attachments/assets/c274e9a4-112f-4228-8fe6-b1ab7ba7f99e)

### 3. Verification request with an invalid OTP

![Email OTP request containing an incorrect code](https://github.com/user-attachments/assets/8c3473c4-298f-456c-baf0-3ec082e5ad56)

### 4. Server rejects the invalid OTP

![Invalid OTP rejected by the server](https://github.com/user-attachments/assets/ac594719-0a7a-421a-9de4-02034846c160)

### 5. Failure response replaced with success

![OTP response manually modified in Burp](https://github.com/user-attachments/assets/f3c54456-6ee9-470e-86fa-6db6ffcd4c5d)

### 6. Registration form becomes accessible

![Registration form after the response manipulation](https://github.com/user-attachments/assets/f8ebb912-0845-4555-ad8e-29fc8535b102)

### 7. Dashboard after registration

![Dashboard after completing registration](https://github.com/user-attachments/assets/933c73a5-df61-48a6-a240-52737b10a7de)

## Impact

- The browser advances past email verification after receiving the modified response.
- The registration flow ends with an account-created notification and dashboard access.
- If the backend permits this without verifying email ownership, unverified registrations and abuse of email-based trust may be possible.

Backend verified-email status and account takeover have not been confirmed.

## Remediation

1. Enforce server-side email verification before account activation and protected access.
2. Bind verification state to the email and registration session; never trust a client-visible success response.
3. Use short-lived, single-use OTPs and rate-limit generation and verification attempts.
