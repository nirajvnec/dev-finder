public class MailDlValidator : AbstractValidator<MailDl>  // ← MailDl from Repository project
{
    public MailDlValidator()
    {
        RuleFor(x => x.MailDlName)
            .NotEmpty()
            .WithMessage("Mail DL name is required");
    }
}