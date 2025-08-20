RuleFor(e => e.Subscription)
    .NotNull()
    .WithMessage("Each event must have a subscription.")
    .ChildRules(subscription =>
    {
        subscription.RuleFor(s => s.SubscriptionName)
            .NotEmpty()
            .WithMessage("Subscription name is missing.");
    });