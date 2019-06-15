---
title: How to validate your commands
description: Overview of the fluent interface to validate commands
keywords: Overview, Validation, Command, FluentValidation, CommandInputValidator, CommandBusinessValidator, AbstractValidator, Rules, IRuleImplementationFor
author: michael
---
Dolittle adheres rigidly to the Separation of Concerns and Single Responsibilty principles.  When executing a Command,
there are a whole raft of steps that must happen in order for the Command to succeed.  These steps are identified as 
separate concerns / responsibilities and are implemented in as isolated and focussed a manner as possible.

Validation of a Command is a domain-specific concern.  It has to be implemented by the developer.  However, the platform can
help by making it easier to write code that deals with validation and also by ensuring that that the validator is discovered,
instantiated and called at the correct point in the Command-processing pipeline.

In Dolittle, Command validation has two flavours, each of which fulfills a different purpose.

- Input Validation
- Business Validation

## 1 - Input Validation

Input Validation is simply the process of making sure that the command is well-formed.  That is, the command has all the things that it needs to succeed and that they are in the correct format / range, etc.  The purpose of Input Validation is to fail as quickly as possible when we know that the command cannot succeed.  

Examples of input validation rules would be:
- required fields
- a number is within a range
- the value is from a set of predefined options (e.g. an Enum)
- a string satisfies a particular format (e.g. regex expression)

When we know that a required field is not provided, or that the email is not of the correct format, we can short-circuit the command processing and give quicker and more focussed feedback to the user/developer.

## 2 - Business Validation

Business validation is making sure that the intended action that the Command represents is valid given the existing state of the sytem.  While the Aggregate Root is the ultimate arbiter of whether an action is possible (it maintains its own invariants) a business validator can provide support and the type of set based validation that an Aggregate cannot do.

Examples of business validation rules would be:
- does the customer exist?
- is the email address unique or has it been used before?
- is it possible to cancel the order given its current state?

Business validation fires after input validation.  Therefore, Business Validation only fires if the Command has been found to be well formed.  It is not required to check for nulls or suchlike within your business validator.  This is not the responsibility of the Business Validator and you should assume that it has already been done.

### Implementing an Input Validator in Dolittle

The simplest way to implement an Input Validator in Dolittle is to inherit from the `CommandInputValidatorFor<T>` class where T is the Command type you wish to validate.  The `CommandInputValidatorFor<T>` is found within the `Dolittle.Commands.Validation` namespace.

{{% notice tip %}} 
You can always implement the `ICommandInputValidator` directly.  The Dolittle implementation relies on the <a href="https://fluentvalidation.net/" target="_blank">FluentValidation</a> library to implement the validation rules.  FluentValidation is a powerful and easy to use library.
{{% /notice %}}

The input validator will be discovered, associated with the Command type, instantiated and applied to all instances of the command that are processed.  Validation rules are defined in the constructor of the Validator via the fluent interface provided by FluentValidation.

Assuming that we have a Command `RegisterCustomer` which requires an Id (e.g. a Guid) and an email address, then an Input validator might look like this:

```csharp
using System;
using Dolittle.Commands;
using Dolittle.Commands.Validation;
using FluentValidation;

namespace Domain.Customers
{

    public class RegisterCustomerInputValidator : CommandInputValidatorFor<RegisterCustomer>
    {
        public RegisterCustomerInputValidator()
        {
            RuleFor(_ => _.Id)
                .NotEmpty().WithMessage("You must provide a valid customer id");
            RuleFor(_ => _.Email)
                .EmailAddress().WithMessage("Must be a valid email address");
        }
    }
}
```
`NotEmpty` is a method provided by FluentValidation that equates to "Not null and not the default value of the type".

### Reusing validators

Dolittle highly encourages the use of strongly typed concepts in place of primitives.  It is recommended that you create validators for your concepts rather than duplicate the setup of the validation rules.

In the above example, the `CustomerId` is better expressed as a Concept and it likely to be present on a whole number of other commands.  You can create a validator for the `CustomerId` and extend the FluentValidation fluent interface to make it easier to compose your validation rules across commands.

```csharp
using FluentValidation;

namespace Concepts.Customers
{
    /// <summary>
    /// Validates an CustomerId to make sure it is well formed
    /// </summary>
    public class CustomerIdValidator : AbstractValidator<CustomerId>
    {
        /// <summary>
        /// Instantiates an instance of a <see cref="CustomerIdValidator" />
        /// </summary>
        public CustomerIdValidator()
        {
            RuleFor(_ => _.Value)
                .NotEmpty()
                .WithMessage("The Customer Id cannot be empty");
        }
    }

    /// <summary>
    /// Extensions to make it easier to include Concept validators in Input Validators
    /// </summary>
    public static partial class ValidatorBuilderExtensions 
    {
        /// <summary>
        /// Adds an CustomerIdValidator and a Null Check to an CustomerId
        /// </summary>
        /// <typeparam name="T">Type of the Command</typeparam>
        /// <param name="ruleBuilder">instance of the IRuleBuilder</param>
        /// <param name="isOptional">flag to indicate if the <see cref="CustomerIdValidator" /> is optional on the command</param>
        /// <returns></returns>
        public static IRuleBuilderOptions<T, CustomerId> MustBeAValidCustomerId<T>(this IRuleBuilder<T, CustomerId> ruleBuilder, bool isOptional = false) 
        {
            if(!isOptional)
			    ruleBuilder.NotNull().WithMessage("A CustomerId is required");
            return ruleBuilder.SetValidator(new CustomerIdValidator());
		}
    }       
}
```

The validation rule for the `CustomerId` is expressed in the `CustomerIdInputValidator` and is simply that you must provide a valid Guid (not an Empty Guid).  The fluent interface is extended to provide a descriptive and easily discoverable method name `MustBeAValidCustomerId`.    Internally, this method does two things.  It sets the validator for this property to be the `CustomerIdInputValidator` that you created.  Optionally, it adds a check to see if the `CustomerId` is null.  The way FluentValidation is implemented, a validator that you set will only be fired if the thing it is validating is not null (this is why you cannot put the null check directly into the `CustomerIdInputValidator`).  With the default behaviour being that the `CustomerId` is not optional, our `RegisterCustomerInputValidator` could be re-written as:

```csharp
using System;
using Dolittle.Commands;
using Dolittle.Commands.Validation;
using FluentValidation;
using Concepts.Customers;

namespace Domain.Customers
{
    public class RegisterCustomerInputValidator : CommandInputValidatorFor<RegisterCustomer>
    {
        public RegisterCustomerInputValidator()
        {
            RuleFor(_ => _.Id).MustBeAValidCustomerId();
            RuleFor(_ => _.Email).MustBeAValidEmailAddress();
        }
    }
}
```

It is possible to create base validators and inherit from them for when you have complex and repeated validation.  This should be done sparingly and with much consideration.  It is not, for example, recommended to create a base `CustomerCommandInputValidator` that validates the `CustomerId` property.  It is better to compose your validations via the fluent interface than create complex inheritance hierarchies.

### Implementing a Business Validator in Dolittle

The simplest way to implement a Business Validator in Dolittle is to inherit from the `CommandBusinessValidatorFor<T>` class where T is the Command type you wish to validate.  The `CommandBusinessValidatorFor<T>` is found within the `Dolittle.Commands.Validation` namespace.

{{% notice tip %}} 
You can always implement the `ICommandBusinessValidator` directly.
{{% /notice %}}

The business validator will be discovered, associated with the Command type, instantiated and applied to all instances of the command that are processed.  Validation rules are defined in the constructor of the Validator via the fluent interface provided by FluentValidation.

Given the command `RegisterCustomer` which requires an Id (e.g. a Guid) and an email address, then a Business validator might look like this:

```csharp
using System;
using Dolittle.Commands;
using Dolittle.Commands.Validation;
using FluentValidation;
using Concepts.Customers;

namespace Domain.Customers
{
    public class RegisterCustomerBusinessValidator : CommandBusinessValidatorFor<RegisterCustomer>
    {
        readonly CustomerExists _customerExists;
        readonly EmailExists _emailExists;
        public RegisterCustomerBusinessValidator(CustomerExists customerExists, EmailExists emailExists)
        {
            _customerExists = customerExists;
            _emailExists = emailExists;
            RuleFor(_ => _.Id).Must(NotBeAnExistingCustomer)
                .WithMessage("This customer already exists");
            RuleFor(_ => _.Email).Must(BeAUniqueEmail)
                .WithMessage("This email address is already in use");
        }

        bool NotBeAnExistingCustomer(CustomerId id)
        {
            return !_customerExists(id);
        }  

        bool BeAUniqueEmail(EmailAddress email)
        {
            return !_emailExists(email);
        }   
    }
}
```

Since the business rules that we are validating here (that the customer with this id is not already registered and that the email address is not already in use) are both set based validators, the Domain side does not generally implement this rule itself.  It defines delegates that express the rule and then requires implementations of these rules (generally utilising the Read side) to be provided (via dependency injection).

The rules will be expressed in the Domain as such:

```csharp
using Concepts.Customers;

namespace Domain.Customers
{
    public delegate bool CustomerExists(CustomerId customerId);
    public delegate bool EmailExists(EmailAddress email);
}
```

This is such a common pattern that we have provided helpers for writing and hooking up your rule implementations to your rules.

The Rules project should have a reference to both the Domain and Read projects.  By implementing the `IRuleImplementationFor<>` interface for the specific delegate, the platform will discover and provide the implementation of the rule to any validator requiring it.

```csharp
using Concepts.Customers;
using Dolittle.DependencyInversion;
using Dolittle.Rules;
using Domain.Customers;
using Read;

namespace Rules.Customers
{
    public class GetCustomerExists : IRuleImplementationFor<CustomerExists>
    {
        private readonly FactoryFor<IReadModelRepository<Read.Customers.Customer>> _getCustomerRepo;

        public GetCustomerExists(FactoryFor<IReadModelRepository<Customer> getCustomerRepo)
        {
            _getCustomerRepo = getCustomerRepo;
        }

        public CustomerExists Rule => (customerId) => _getCustomerRepo().Any(_ => _.Id == customerId);
    }
}
```

The rule implementation simply queries the read side to see if any customers with this Id exist.  Note that the implementation does not
take an IReadModelRepository<Customer> directly.  This is because resources like databases are scoped to tenants and in a multi-tenanted app you need to make sure you are given the correctly scoped dependency. By using `FactoryFor<>` to request an instance of the repository each time you use it, you will always have the correct repository for the current tenant.

