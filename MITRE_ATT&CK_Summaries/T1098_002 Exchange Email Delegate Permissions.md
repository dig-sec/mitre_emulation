# Alerting & Detection Strategy: Exchange Email Delegate Permissions (T1098.002)

---

## Goal
The goal of this technique is to detect adversarial attempts to exploit email delegation permissions on Microsoft Exchange Server as part of their efforts to establish persistence in a network. This involves granting full access mailbox permissions to unauthorized users, enabling them to bypass security monitoring and gain sensitive information.

## Categorization

- **MITRE ATT&CK Mapping:** T1098.002 - Exchange Email Delegate Permissions
- **Tactic / Kill Chain Phases:** Persistence
- **Platforms:** Windows, Office 365
- [MITRE ATT&CK Reference](https://attack.mitre.org/techniques/T1098/002)

## Strategy Abstract
This detection strategy leverages multiple data sources within the Office 365 environment to monitor and analyze patterns related to the exchange of email delegate permissions. Key data sources include:

- **Exchange Online Activity Monitoring (EOMM):** Logs user actions regarding mailbox management, including permission changes.
- **Security & Compliance Center (SCC):** Provides audit logs for tracking configuration changes in mailboxes.
- **PowerShell Logging:** Captures command usage related to Exchange and Office 365, particularly the `Add-MailboxPermission` cmdlet.

The strategy focuses on identifying unusual or unauthorized alterations to delegate permissions. Patterns include unexpected permission grants, high-frequency changes, and anomalies such as permissions given by users without administrative roles or outside their usual behavior patterns.

## Technical Context
Adversaries may exploit Exchange Email Delegate Permissions to maintain persistence within an organization's network. By granting full access mailbox permissions to compromised accounts or newly created user accounts, adversaries can read sensitive emails, impersonate users, and evade detection by security solutions that overlook delegated permissions.

**Execution Method:**
1. Compromise a non-administrative account with sufficient privileges.
2. Use Exchange Management Shell commands such as:
   ```powershell
   Add-MailboxPermission -Identity "target@domain.com" -User "attacker@domain.com" -AccessRights FullAccess -InheritanceType All
   ```
3. Monitor compromised mailbox activities for sensitive data extraction.

## Blind Spots and Assumptions
- **Blind Spots:**
  - Difficulty in distinguishing between legitimate business needs for permissions changes versus malicious intent.
  - Encrypted traffic may obscure some PowerShell commands or logs.
  
- **Assumptions:**
  - Assumes that all relevant log sources are properly configured to capture delegate permission activities.
  - Relies on the availability of historical behavior data to establish baseline patterns.

## False Positives
Potential benign scenarios triggering false alerts include:
- Legitimate IT operations where administrators regularly modify permissions for maintenance or support tasks.
- User requests to grant access for project collaborations that require temporary full access delegation.
- Automated processes configured by IT teams that periodically adjust mailbox settings.

## Priority
**Priority: High**

**Justification:** 
Granting unauthorized full access mailbox permissions can lead to significant data breaches and lateral movement within an organization. The potential impact includes loss of sensitive information, financial damage, and reputational harm. Therefore, prompt detection and response are critical.

## Validation (Adversary Emulation)
To emulate this technique in a test environment:

1. **Setup:**
   - Ensure you have administrative access to the Office 365 tenant.
   - Configure logging for Exchange Online Activity Monitoring and Security & Compliance Center.

2. **Emulate Attack:**
   - Use a non-administrative user account with sufficient permissions.
   - Execute the following PowerShell command:
     ```powershell
     Add-MailboxPermission -Identity "victim@domain.com" -User "attacker@domain.com" -AccessRights FullAccess -InheritanceType All
     ```
   - Verify that the change is logged in EOMM and SCC.

3. **Observation:**
   - Monitor for alerts generated by your detection strategy.
   - Validate logs to ensure changes are captured accurately.

## Response
When an alert indicating unauthorized delegate permission changes fires, analysts should:

1. **Verify the Alert:** Confirm that the detected change is not part of routine IT operations or authorized tasks.
2. **Investigate User Activity:**
   - Review recent activities of both the user who made the change and the recipient account for unusual behavior.
3. **Containment:**
   - Revoke unauthorized permissions immediately if malicious intent is suspected.
4. **Notification:**
   - Inform relevant stakeholders, including IT security teams and potentially affected business units.
5. **Remediation:**
   - Conduct a thorough review of mailbox permission settings across the organization to identify and rectify similar vulnerabilities.

## Additional Resources
- Office 365 documentation on [Exchange Online Activity Monitoring](https://docs.microsoft.com/en-us/microsoft-365/compliance/office-365-audit-log-investigation?view=o365-worldwide)
- Guides for configuring [Security & Compliance Center](https://docs.microsoft.com/en-us/microsoft-365/compliance/set-up-your-security-and-compliance-center?view=o365-worldwide)

---

This Markdown report outlines the detection strategy for monitoring unauthorized Exchange Email Delegate Permissions, providing a structured approach to identify and respond to potential security threats.