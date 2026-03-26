# CIPP Integration Guide for Essential Eight Drift Management

This guide explains how to use CIPP (CyberDrain Integrated Platform for Partners) to manage configuration drift for your ACSC Essential Eight assessment system.

## Overview

CIPP provides **Standards Templates** that can monitor and remediate configurations across your Microsoft 365 tenants. By creating a template that maps to your Essential Eight requirements, you can:

1. **Detect drift** - Get alerted when configurations deviate from your baseline
2. **Auto-remediate** - Automatically fix non-compliant settings
3. **Report compliance** - Generate reports for stakeholders

## Files Included

- `cipp_essential_eight_template.json` - Comprehensive template with full documentation
- `cipp_template_import.json` - Simplified import format for CIPP
- `CIPP_INTEGRATION_GUIDE.md` - This guide

## Essential Eight Coverage Mapping

### P3 - Multi-Factor Authentication

| CIPP Standard | E8 Requirement | Action |
|--------------|----------------|--------|
| `standards.EnableFIDO2` | P3R10-R12, P3R20-R23 (Phishing-resistant MFA) | Remediate |
| `standards.EnableHardwareOAuth` | P3R10-R12, P3R20-R23 (Hardware tokens) | Remediate |
| `standards.DisableSMS` | P3R10-R12 (Block non-phishing-resistant) | Remediate |
| `standards.DisableVoice` | P3R10-R12 (Block non-phishing-resistant) | Remediate |
| `standards.NudgeMFA` | P3R1-R7 (Drive MFA adoption) | Remediate |
| `standards.PerUserMFA` | P3R1-R7 (Track MFA status) | Report |
| `standards.AuditLog` | P3R13-R19 (Event logging) | Remediate |
| `standards.EnableMailboxAuditing` | P3R13-R19 (Mailbox audit trails) | Remediate |

### P1/P2 - Patch Applications & Operating Systems

| CIPP Standard | E8 Requirement | Action |
|--------------|----------------|--------|
| `standards.StaleEntraDevices` | P1/P2 (Remove stale devices from patching) | Remediate |
| `standards.EnableCustomerLockbox` | P1/P2 (Control vendor access for patches) | Remediate |

**Note:** Patch management is primarily handled via Intune policies (see below), not CIPP standards.

### P6 - Macro Restrictions

P6 requirements are satisfied via **Intune ASR Rules** templates, which should be deployed separately through Intune or via CIPP's Intune template feature.

### P7 - Application Hardening

| CIPP Standard | E8 Requirement | Action |
|--------------|----------------|--------|
| `standards.OauthConsent` | P7R7 (Block unauthorized apps) | Remediate |
| `standards.DisableBasicAuthSMTP` | P7R4 (Disable legacy protocols) | Remediate |
| `standards.PhishProtection` | P7R5 (Anti-phishing branding) | Remediate |
| `standards.ActivityBasedTimeout` | P7R3 (Session management) | Remediate |
| `standards.RestrictThirdPartyStorage` | P7R6 (Data exfiltration prevention) | Remediate |
| `standards.FormsPhishingProtection` | P7R5 (Form phishing protection) | Remediate |
| `standards.laps` | P7R2 (Privileged access management) | Remediate |
| `standards.PasswordExpireDisabled` | P7R1 (Modern auth guidance) | Remediate |

## How to Import into CIPP

### Step 1: Create a New Template

1. Log into CIPP
2. Navigate to **Standards > Templates**
3. Click **Add Standards Template**
4. Enter template name: "ACSC Essential Eight Baseline"

### Step 2: Add Standards

For each standard in `cipp_template_import.json`:

1. Select the appropriate standard from the dropdown
2. Set the action:
   - **Remediate** - Automatically fix non-compliant settings
   - **Report** - Just report drift, don't auto-fix
   - **Warn** - Alert but don't change
3. Configure any required values
4. Add notes linking to Essential Eight requirements

### Step 3: Configure Drift Detection

1. Enable **Drift Detection**
2. Set notification email(s)
3. Configure webhook (optional - can integrate with your assessment system)
4. Set schedule (recommend daily for security-critical items)

### Step 4: Apply to Tenants

1. Select the tenants to apply this template to
2. Click **Save Template**

## Intune Policy Templates

CIPP also supports Intune templates. Create these in **Intune > Templates**:

### Template 1: ASR Rules (P6/P7)

```json
{
  "displayName": "E8 - ASR Rules for Macro Hardening",
  "description": "Attack Surface Reduction rules for P6 Macro Restrictions and P7 Application Hardening",
  "roleScopeTagIds": [],
  "deviceManagementApplicabilityRuleOsEdition": null,
  "deviceManagementApplicabilityRuleOsVersion": null,
  "deviceManagementApplicabilityRuleDeviceMode": null,
  "settings": {
    "@odata.type": "#microsoft.graph.windows10EndpointProtectionConfiguration",
    "defenderOfficeAppsExecutableContentCreation": "block",
    "defenderOfficeAppsExecutableContentCreationDropbox": "block",
    "defenderOfficeAppsExecutableContentCreationOneDrive": "block",
    "defenderOfficeAppsExecutableContentCreationNetworkShare": "block",
    "defenderOfficeAppsExecutableContentCreationRemovableDrive": "block",
    "defenderOfficeAppsExecutableContentCreationEmail": "block",
    "defenderOfficeAppsProcessCreation": "block",
    "defenderOfficeAppsProcessCreationDropbox": "block",
    "defenderOfficeAppsProcessCreationOneDrive": "block",
    "defenderOfficeAppsProcessCreationNetworkShare": "block",
    "defenderOfficeAppsProcessCreationRemovableDrive": "block",
    "defenderOfficeAppsProcessCreationEmail": "block",
    "defenderOfficeCommunicationAppsProcessCreation": "block",
    "defenderOfficeAppsChildProcessCreation": "block",
    "defenderOfficeAppsChildProcessCreationDropbox": "block",
    "defenderOfficeAppsChildProcessCreationOneDrive": "block",
    "defenderOfficeAppsChildProcessCreationNetworkShare": "block",
    "defenderOfficeAppsChildProcessCreationRemovableDrive": "block",
    "defenderOfficeAppsChildProcessCreationEmail": "block",
    "defenderOfficeAppsOtherProcessInjection": "block",
    "defenderOfficeAppsOtherProcessInjectionDropbox": "block",
    "defenderOfficeAppsOtherProcessInjectionOneDrive": "block",
    "defenderOfficeAppsOtherProcessInjectionNetworkShare": "block",
    "defenderOfficeAppsOtherProcessInjectionRemovableDrive": "block",
    "defenderOfficeAppsOtherProcessInjectionEmail": "block",
    "defenderAdvancedRansomewareProtectionType": "enable",
    "defenderNetworkProtectionType": "enable"
  }
}
```

### Template 2: Patch Management (P1/P2)

```json
{
  "displayName": "E8 - Windows Update for 48hr Critical Patching",
  "description": "Windows Update policy to meet Essential Eight 48-hour critical patching requirement",
  "settings": {
    "@odata.type": "#microsoft.graph.windowsUpdateForBusinessConfiguration",
    "deliveryOptimizationMode": "httpWithPeeringNat",
    "automaticUpdateMode": "autoInstallAtMaintenanceTime",
    "microsoftUpdateServiceAllowed": true,
    "qualityUpdatesDeferralPeriodInDays": 0,
    "featureUpdatesDeferralPeriodInDays": 14,
    "updateWeeks": null,
    "userPauseAccess": "disabled",
    "userWindowsUpdateScanAccess": "enabled",
    "updateNotificationLevel": "restartOnly",
    "installationBehavior": {
      "@odata.type": "microsoft.graph.win32LobAppInstallation",
      "installTimeoutInMinutes": 60,
      "allowAvailableUninstall": false
    },
    "expediteUpdatesForXDays": 7
  }
}
```

### Template 3: Device Compliance

```json
{
  "displayName": "E8 - Device Compliance",
  "description": "Device compliance policy for Essential Eight requirements",
  "settings": {
    "@odata.type": "#microsoft.graph.windows10CompliancePolicy",
    "passwordRequired": true,
    "passwordBlockSimple": true,
    "passwordRequiredToUnlockFromIdle": true,
    "passwordMinutesOfInactivityBeforeLock": 15,
    "passwordExpirationDays": null,
    "passwordMinimumLength": 14,
    "passwordMinimumCharacterSetCount": 3,
    "passwordRequiredType": "alphanumeric",
    "passwordPreviousPasswordBlockCount": 24,
    "requireHealthyDeviceReport": true,
    "osMinimumVersion": "10.0.19041",
    "osMaximumVersion": null,
    "mobileOsMinimumVersion": null,
    "mobileOsMaximumVersion": null,
    "earlyLaunchAntiMalwareDriverEnabled": true,
    "bitLockerEnabled": true,
    "secureBootEnabled": true,
    "codeIntegrityEnabled": true,
    "storageRequireEncryption": true,
    "activeFirewallRequired": true,
    "defenderEnabled": true,
    "signatureOutOfDate": true,
    "rtpEnabled": true,
    "antivirusRequired": true,
    "antiSpywareRequired": true,
    "defenderVersion": "4.18.2109.6",
    "defenderUpdateSignature": true
  }
}
```

## Conditional Access Templates

For P3 MFA requirements, use CIPP's Conditional Access template feature:

### Template: MFA for All Users

```json
{
  "displayName": "E8 P3R1 - Require MFA for All Users",
  "state": "enabled",
  "conditions": {
    "applications": {
      "includeApplications": ["All"]
    },
    "users": {
      "includeUsers": ["All"]
    }
  },
  "grantControls": {
    "operator": "OR",
    "builtInControls": ["mfa"]
  }
}
```

### Template: Admin Phishing-Resistant MFA

```json
{
  "displayName": "E8 P3R10-R12 - Admin Phishing-Resistant MFA",
  "state": "enabled",
  "conditions": {
    "applications": {
      "includeApplications": ["All"]
    },
    "users": {
      "includeRoles": [
        "62e90394-69f5-4237-9190-012177145e10",
        "e8611ab8-c189-46e8-94e1-60213ab1f814",
        "fe930be7-5e62-47db-91af-98c3a49a38b1"
      ]
    }
  },
  "grantControls": {
    "operator": "OR",
    "authenticationStrength": {
      "id": "00000000-0000-0000-0000-000000000004"
    }
  }
}
```

## Integration with Your Assessment System

### Option 1: Webhook Integration

Configure CIPP drift alerts to POST to your assessment system's API:

```json
{
  "webhookUrl": "https://your-assessment-system.com/api/cipp-drift",
  "webhookHeaders": {
    "Authorization": "Bearer YOUR_API_KEY"
  }
}
```

Your system can then:
- Receive drift notifications
- Automatically update assessment findings
- Trigger re-assessment for affected requirements

### Option 2: Scheduled Export

Use CIPP's scheduled reports to export compliance data:

1. **Standards Report** - Export all standard compliance status
2. **Compare tenant** - Compare tenant against baseline
3. **Import into your DB** - Parse and store in your assessment database

### Option 3: API Integration

Use CIPP's API to pull compliance data:

```powershell
# Get standards compliance for a tenant
$headers = @{
    "Authorization" = "Bearer $CIPP_TOKEN"
}

$compliance = Invoke-RestMethod `
    -Uri "https://your-cipp-instance/api/ExecListStandards?TenantFilter=tenant.onmicrosoft.com" `
    -Headers $headers

# Process and update your assessment system
```

## Monitoring Drift

### Drift Report Columns

| Column | Description |
|--------|-------------|
| Standard | CIPP standard name |
| Current Value | Actual tenant setting |
| Expected Value | Template requirement |
| Status | Compliant / Non-Compliant |
| Last Checked | Timestamp |
| Essential Eight | Mapped E8 requirement |

### Recommended Alert Thresholds

| Severity | Response Time | Example |
|----------|-------------|---------|
| Critical | 24 hours | MFA disabled, Audit logging off |
| High | 72 hours | Legacy auth enabled, ASR rules changed |
| Medium | 7 days | Session timeout changed, Device compliance drift |
| Low | 30 days | Password policy drift |

## Best Practices

1. **Start with Report Mode** - Run new templates in Report-only mode for 2 weeks before enabling Remediate

2. **Test on Dev Tenant** - Always test templates on a development tenant first

3. **Document Exceptions** - Use CIPP's exclusion feature for legitimate exceptions

4. **Version Control** - Store your template JSON in Git alongside your assessment code

5. **Regular Review** - Review template compliance monthly and adjust as E8 requirements evolve

6. **Coordinate with Patching** - Ensure Intune patch policies align with CIPP device standards

## Troubleshooting

### Standard Not Applying

1. Check CIPP logs for permission errors
2. Verify the standard is available in your CIPP version
3. Check for conflicting standards

### False Positives

1. Review the standard's `addedComponent` in CIPP
2. Check if values are case-sensitive
3. Verify tenant has required licenses

### Drift Alerts Not Sending

1. Check email configuration in CIPP
2. Verify webhook URL is accessible from CIPP
3. Check CIPP logs for notification errors

## References

- [CIPP Standards Documentation](https://docs.cipp.app/user-documentation/tenant/standards/template)
- [CIPP Standards List](https://docs.cipp.app/user-documentation/tenant/standards/list-standards/classic-standards)
- [ACSC Essential Eight](https://www.cyber.gov.au/acsc/view-all-content/programs/essential-eight)
- [Microsoft Security Defaults](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals/concept-fundamentals-security-defaults)

## Support

For CIPP-specific issues:
- CIPP Discord: https://discord.gg/cyberdrain
- CIPP GitHub Issues: https://github.com/KelvinTegelaar/CIPP/issues

For Essential Eight assessment questions, refer to your assessment system documentation.
