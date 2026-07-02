---
Title: Look Alike Domains
---

:::warning
THIS DOCUMENTATION IS STILL IN DEVELOPMENT AND SUBJECT TO CHANGE.
:::

## Look-Alike Domains Manual

## Introduction

Look-alike domains are deceptive URLs that closely resemble legitimate domains. Cybercriminals use these domains to trick users into believing they are interacting with a trusted entity, often for phishing attacks or distributing malware.

If your organization is the owner of `example.com`, Flare will spot the following look-alike domains (and many others):

- `examp1e.com`
- `exemple.com`
- `exqmple.com`
- `exanple.com`
- `exmaple.com`
- `exampl.com`
- `examples.com`
- `example.ca`
- `example.io`
- `ex.ample.com`
- `example.payment.com`

## Identifying Look-Alike Domains

### Common Techniques

1. **Character Substitution**: Replacing characters with similar-looking ones (e.g., using '0' instead of 'O').
2. **Homoglyphs**: Using characters from different scripts that look similar (e.g., Cyrillic 'а' instead of Latin 'a').
3. **Typosquatting**: Registering common misspellings of a legitimate domain (e.g., 'gogle.com' instead of 'google.com').
4. **Combosquatting**: Adding words to the domain (e.g., 'secure-google.com').

## Monitoring Look-Alike Domains

### Tools and Techniques

1. **Domain Monitoring Services**: Use services that alert you when new domains similar to your brand are registered.
2. **Certificate Transparency Logs**: Monitor these logs for certificates issued to look-alike domains.
3. **Manual Checks**: Regularly search for variations of your domain name.

## Mitigating Risks

### Best Practices

1. **Register Similar Domains**: Proactively register domains that are similar to your legitimate domain.
2. **Educate Users**: Train employees and customers to recognize look-alike domains.
3. **Implement DMARC**: Use DMARC, DKIM, and SPF to protect your domain from being spoofed.
4. **Use Browser Extensions**: Encourage the use of browser extensions that detect phishing sites.

## Responding to Look-Alike Domains

### Steps to Take

1. **Report to Domain Registrars**: Notify the registrar of the look-alike domain to request its suspension.
2. **Legal Action**: Consider legal action against the registrant of the look-alike domain.
3. **Notify Users**: Inform your users about the look-alike domain and advise them to avoid it.

## Conclusion

Vigilance and proactive measures are key to protecting your organization from the threats posed by look-alike domains. Regular monitoring, user education, and swift action can help mitigate these risks.

For more details, visit the official documentation: [Flare.io look-alike-domains](https://docs.flare.io/look-alike-domains).
