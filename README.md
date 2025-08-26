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