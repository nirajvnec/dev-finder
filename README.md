
async function isReportValidForSave(data: ReportRequestModel): Promise<boolean> {
    const newErrMessage: string[] = [];
    let isValid = true;

    // Existing validations...
    if (reportNameValidationmsg(data.reportName)) {
        newErrMessage.push(`Report Name: ${reportNameValidationmsg(data.reportName)}`);
        isValid = false;
    }

    if (reportDescrpValidationmsg(data.reportDescription)) {
        newErrMessage.push(`Report Description: ${reportDescrpValidationmsg(data.reportDescription)}`);
        isValid = false;
    }

    if (data.reportDatasets.length === 0 && data.isPortfolioReport === false) {
        newErrMessage.push('Select at least one dataset.');
        isValid = false;
    }

    if (data.reportSubReports.length === 0 && data.isPortfolioReport === true) {
        newErrMessage.push('Select at least one report.');
        isValid = false;
    }

    // **Call the new helper for email validations**
    const emailErrors = validateEmailSubscriptions(data.reportSubscriptions);
    if (emailErrors.length > 0) {
        newErrMessage.push(...emailErrors);
        isValid = false;
    }

    if (!isValid) {
        console.error('Validation errors:', newErrMessage);
    }

    return isValid;
}






function validateEmailSubscriptions(subscriptions: ReportSubscriptionModel[]): string[] {
    let errors: string[] = [];
    let emailToMissing = false;
    let emailCcMissing = false;

    if (!subscriptions || subscriptions.length === 0) return errors;

    subscriptions.forEach(subscription => {
        if (subscription.reportDeliveryModeKey === 2) { // Email delivery mode
            if (!subscription.emailTo || subscription.emailTo.length === 0) {
                emailToMissing = true;
            }
            if (!subscription.emailCc || subscription.emailCc.length === 0) {
                emailCcMissing = true;
            }
        }
    });

    if (emailToMissing) errors.push('Email To is required when delivery mode is Email.');
    if (emailCcMissing) errors.push('Email CC is required when delivery mode is Email.');

    return errors;
}