feat: add dynamic generic repository architecture

- Create GenericRepository<TEntity, TKey> with EF Core integration
- Implement EntityServiceFactory to route dynamic entity operations
- Add MailDlService with FluentValidation for business rules
- Add EntityTypeProvider to map string names to entity types
- Fix ID conversion handling between API (string) and repository (typed)
- Set up DI registrations for complete generic CRUD workflow