function validateEmailSubscriptions(subscriptions: ReportSubscriptionModel[]): string[] {
    const errors: string[] = [];
    let missingRecipient = false;

    if (!subscriptions || subscriptions.length === 0) return errors;

    subscriptions.forEach(subscription => {
        if (subscription.reportDeliveryModeKey === 2) { // Email delivery mode
            const hasEmailTo = subscription.emailTo && subscription.emailTo.length > 0;
            const hasEmailCc = subscription.emailCc && subscription.emailCc.length > 0;

            if (!hasEmailTo && !hasEmailCc) {
                missingRecipient = true;
            }
        }
    });

    if (missingRecipient) {
        errors.push('Select at least one email recipient.');
    }

    return errors;
}



// Call the helper for email validations
const emailErrors = validateEmailSubscriptions(data.reportSubscriptions);
if (emailErrors.length > 0) {
    newErrMessage.push(...emailErrors);
    isValid = false;
}