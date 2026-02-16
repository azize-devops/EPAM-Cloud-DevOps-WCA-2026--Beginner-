# OPERATORS AND REGULAR EXPRESSIONS - ANSWER

This guide covers PowerShell operators and regular expressions (regex)
for pattern matching, string manipulation, and validation.


## OPERATORS AND REGEX OVERVIEW

Comparison operators:

| Operator | Description                    | Example                    |
|----------|--------------------------------|----------------------------|
| -eq      | Equal                          | 5 -eq 5                    |
| -ne      | Not equal                      | 5 -ne 3                    |
| -gt      | Greater than                   | 5 -gt 3                    |
| -lt      | Less than                      | 3 -lt 5                    |
| -ge      | Greater or equal               | 5 -ge 5                    |
| -le      | Less or equal                  | 3 -le 5                    |
| -like    | Wildcard match                 | "hello" -like "h*"         |
| -match   | Regex match                    | "hello" -match "^h"        |
| -replace | Regex replace                  | "cat" -replace "c","b"     |

Regex special characters:

| Pattern  | Description                    | Example                    |
|----------|--------------------------------|----------------------------|
| ^        | Start of string                | ^hello                     |
| $        | End of string                  | world$                     |
| .        | Any single character           | h.llo                      |
| *        | Zero or more                   | ab*c                       |
| +        | One or more                    | ab+c                       |
| ?        | Zero or one                    | colou?r                    |
| \d       | Digit                          | \d+                        |
| \w       | Word character                 | \w+                        |
| \s       | Whitespace                     | \s+                        |
| [abc]    | Character class                | [aeiou]                    |
| (group)  | Capture group                  | (\d{3})                    |


## TASK 1: Validate Phone Number with Regex

Goal: Validate US phone numbers in format: (XXX) XXX-XXXX

Phone number format requirements:
- Starts with (
- Three digits
- Followed by )
- Space
- Three digits
- Hyphen
- Four digits

Regex pattern:

```powershell
$pattern = '^\(\d{3}\) \d{3}-\d{4}$'
```

Breakdown:
- `^` - Start of string
- `\(` - Literal opening parenthesis
- `\d{3}` - Exactly 3 digits
- `\)` - Literal closing parenthesis
- ` ` - Space
- `\d{3}` - Exactly 3 digits
- `-` - Literal hyphen
- `\d{4}` - Exactly 4 digits
- `$` - End of string

> **Screenshot 1**
>
> Show: Regex pattern explanation
> Expected: Pattern breakdown visible

Validation function:

```powershell
function Test-PhoneNumber {
    param(
        [Parameter(Mandatory=$true)]
        [string]$PhoneNumber
    )

    $pattern = '^\(\d{3}\) \d{3}-\d{4}$'

    if ($PhoneNumber -match $pattern) {
        Write-Host "Valid phone number: $PhoneNumber" -ForegroundColor Green
        return $true
    }
    else {
        Write-Host "Invalid phone number: $PhoneNumber" -ForegroundColor Red
        return $false
    }
}
```

> **Screenshot 2**
>
> Show: Test-PhoneNumber function
> Expected: Function definition

Test valid numbers:

```powershell
Test-PhoneNumber "(123) 456-7890"
# Output: Valid phone number: (123) 456-7890

Test-PhoneNumber "(555) 123-4567"
# Output: Valid phone number: (555) 123-4567

Test-PhoneNumber "(800) 555-0100"
# Output: Valid phone number: (800) 555-0100
```

> **Screenshot 3**
>
> Show: Valid phone number tests
> Expected: Green "Valid" messages

Test invalid numbers:

```powershell
Test-PhoneNumber "123-456-7890"
# Output: Invalid phone number: 123-456-7890

Test-PhoneNumber "(123)456-7890"
# Output: Invalid phone number: (123)456-7890 (missing space)

Test-PhoneNumber "(123) 456-789"
# Output: Invalid phone number: (123) 456-789 (only 3 digits at end)

Test-PhoneNumber "1234567890"
# Output: Invalid phone number: 1234567890
```

> **Screenshot 4**
>
> Show: Invalid phone number tests
> Expected: Red "Invalid" messages

Alternative patterns for different formats:

```powershell
# Multiple format support
$patterns = @(
    '^\(\d{3}\) \d{3}-\d{4}$',      # (123) 456-7890
    '^\d{3}-\d{3}-\d{4}$',          # 123-456-7890
    '^\d{3}\.\d{3}\.\d{4}$',        # 123.456.7890
    '^\d{10}$'                       # 1234567890
)

function Test-PhoneNumber {
    param([string]$PhoneNumber)

    foreach ($pattern in $patterns) {
        if ($PhoneNumber -match $pattern) {
            return $true
        }
    }
    return $false
}
```

> **Screenshot 5**
>
> Show: Multi-format phone validation
> Expected: Different patterns supported


## TASK 2: Validate Email Address with Regex

Goal: Validate email addresses

Email format requirements:
- Username part (letters, numbers, dots, underscores, hyphens)
- @ symbol
- Domain name (letters, numbers, hyphens)
- Dot
- TLD (2+ letters)

Basic regex pattern:

```powershell
$pattern = '^[\w\.-]+@[\w\.-]+\.\w{2,}$'
```

Breakdown:
- `^` - Start of string
- `[\w\.-]+` - One or more word chars, dots, or hyphens
- `@` - Literal @ symbol
- `[\w\.-]+` - One or more word chars, dots, or hyphens
- `\.` - Literal dot
- `\w{2,}` - Two or more word characters (TLD)
- `$` - End of string

> **Screenshot 6**
>
> Show: Email regex pattern
> Expected: Pattern explanation

Validation function:

```powershell
function Test-EmailAddress {
    param(
        [Parameter(Mandatory=$true)]
        [string]$Email
    )

    $pattern = '^[\w\.-]+@[\w\.-]+\.\w{2,}$'

    if ($Email -match $pattern) {
        Write-Host "Valid email: $Email" -ForegroundColor Green
        return $true
    }
    else {
        Write-Host "Invalid email: $Email" -ForegroundColor Red
        return $false
    }
}
```

> **Screenshot 7**
>
> Show: Test-EmailAddress function
> Expected: Function definition

Test valid emails:

```powershell
Test-EmailAddress "user@example.com"
# Output: Valid email: user@example.com

Test-EmailAddress "john.doe@company.org"
# Output: Valid email: john.doe@company.org

Test-EmailAddress "test_user123@domain.co.uk"
# Output: Valid email: test_user123@domain.co.uk

Test-EmailAddress "info@sub.domain.com"
# Output: Valid email: info@sub.domain.com
```

> **Screenshot 8**
>
> Show: Valid email tests
> Expected: Green "Valid" messages

Test invalid emails:

```powershell
Test-EmailAddress "invalid-email"
# Output: Invalid email: invalid-email

Test-EmailAddress "@nodomain.com"
# Output: Invalid email: @nodomain.com

Test-EmailAddress "user@"
# Output: Invalid email: user@

Test-EmailAddress "user@domain"
# Output: Invalid email: user@domain (no TLD)

Test-EmailAddress "user name@domain.com"
# Output: Invalid email: user name@domain.com (space)
```

> **Screenshot 9**
>
> Show: Invalid email tests
> Expected: Red "Invalid" messages

More strict email pattern:

```powershell
$strictPattern = '^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$'
```

> **Screenshot 10**
>
> Show: Strict email pattern
> Expected: RFC-compliant pattern


## TASK 3: Extract Information Using Regex

Goal: Extract specific data from text using capture groups.

Extract domain from email:

```powershell
$email = "user@example.com"
$pattern = '@(.+)$'

if ($email -match $pattern) {
    $domain = $Matches[1]
    Write-Host "Domain: $domain"
    # Output: Domain: example.com
}
```

> **Screenshot 11**
>
> Show: Domain extraction
> Expected: "example.com" extracted

Extract parts of phone number:

```powershell
$phone = "(123) 456-7890"
$pattern = '^\((\d{3})\) (\d{3})-(\d{4})$'

if ($phone -match $pattern) {
    $areaCode = $Matches[1]
    $exchange = $Matches[2]
    $subscriber = $Matches[3]

    Write-Host "Area Code: $areaCode"
    Write-Host "Exchange: $exchange"
    Write-Host "Subscriber: $subscriber"
}
```

Expected output:

```
Area Code: 123
Exchange: 456
Subscriber: 7890
```

> **Screenshot 12**
>
> Show: Phone number parts extraction
> Expected: Three parts extracted

Named capture groups:

```powershell
$phone = "(123) 456-7890"
$pattern = '^\((?<area>\d{3})\) (?<exchange>\d{3})-(?<subscriber>\d{4})$'

if ($phone -match $pattern) {
    Write-Host "Area Code: $($Matches.area)"
    Write-Host "Exchange: $($Matches.exchange)"
    Write-Host "Subscriber: $($Matches.subscriber)"
}
```

> **Screenshot 13**
>
> Show: Named capture groups
> Expected: Named groups used

Extract all matches with Select-String:

```powershell
$text = "Contact us at info@company.com or support@company.com"
$pattern = '[\w\.-]+@[\w\.-]+\.\w+'

$matches = [regex]::Matches($text, $pattern)

foreach ($match in $matches) {
    Write-Host "Found: $($match.Value)"
}
```

Expected output:

```
Found: info@company.com
Found: support@company.com
```

> **Screenshot 14**
>
> Show: Multiple match extraction
> Expected: Both emails found


## COMPLETE SOLUTION SCRIPT

```powershell
# Phone Number Validation
function Test-PhoneNumber {
    param([string]$PhoneNumber)

    $pattern = '^\(\d{3}\) \d{3}-\d{4}$'

    if ($PhoneNumber -match $pattern) {
        Write-Host "Valid phone: $PhoneNumber" -ForegroundColor Green
        return $true
    }
    Write-Host "Invalid phone: $PhoneNumber" -ForegroundColor Red
    return $false
}

# Email Validation
function Test-EmailAddress {
    param([string]$Email)

    $pattern = '^[\w\.-]+@[\w\.-]+\.\w{2,}$'

    if ($Email -match $pattern) {
        Write-Host "Valid email: $Email" -ForegroundColor Green
        return $true
    }
    Write-Host "Invalid email: $Email" -ForegroundColor Red
    return $false
}

# Test phone numbers
Write-Host "`n=== Phone Number Tests ===" -ForegroundColor Cyan
Test-PhoneNumber "(123) 456-7890"
Test-PhoneNumber "123-456-7890"
Test-PhoneNumber "(555) 123-4567"

# Test emails
Write-Host "`n=== Email Tests ===" -ForegroundColor Cyan
Test-EmailAddress "user@example.com"
Test-EmailAddress "invalid-email"
Test-EmailAddress "test@domain.org"
```

> **Screenshot 15**
>
> Show: Complete script with tests
> Expected: All validations working


## COMMON REGEX PATTERNS

| Pattern              | Description                    | Example Match          |
|----------------------|--------------------------------|------------------------|
| `^\d{5}$`            | 5-digit ZIP code               | 12345                  |
| `^\d{5}-\d{4}$`      | ZIP+4 code                     | 12345-6789             |
| `^[A-Z]{2}\d{6}$`    | License plate                  | AB123456               |
| `^\d{3}-\d{2}-\d{4}$`| SSN format                     | 123-45-6789            |
| `^#[0-9A-Fa-f]{6}$`  | Hex color code                 | #FF5733                |
| `^\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}$` | IP address     | 192.168.1.1            |
| `^https?://`         | URL protocol                   | http:// or https://    |


## -REPLACE OPERATOR

Basic replacement:

```powershell
"Hello World" -replace "World", "PowerShell"
# Output: Hello PowerShell

"cat bat rat" -replace "at", "ow"
# Output: cow bow row

"abc123def456" -replace "\d+", "X"
# Output: abcXdefX
```

Using capture groups:

```powershell
# Swap first and last name
"John Doe" -replace "(\w+) (\w+)", '$2, $1'
# Output: Doe, John

# Format phone number
"1234567890" -replace "(\d{3})(\d{3})(\d{4})", '($1) $2-$3'
# Output: (123) 456-7890
```


## TROUBLESHOOTING

Problem: Pattern matches when it shouldn't
Solution: Use anchors ^ and $

```powershell
"abc123" -match "\d+"      # True (partial match)
"abc123" -match "^\d+$"    # False (full match required)
```

Problem: Special characters not matching
Solution: Escape special characters with \

```powershell
"(test)" -match "\(test\)"  # Correct
"(test)" -match "(test)"    # Wrong - creates capture group
```

Problem: Case sensitivity issues
Solution: Use -imatch for case-insensitive

```powershell
"HELLO" -match "hello"      # False (case sensitive)
"HELLO" -imatch "hello"     # True (case insensitive)
```

Problem: $Matches is empty
Solution: Ensure -match succeeded first

```powershell
if ($string -match $pattern) {
    $Matches[1]  # Safe to access
}
```

Problem: Only first match returned
Solution: Use [regex]::Matches for all

```powershell
$allMatches = [regex]::Matches($text, $pattern)
```

> **Screenshot 16**
>
> Show: Summary of regex tasks
> Expected: Both validation functions working


## SELF-REVIEW CHECKLIST

- [ ] Phone number regex validates (XXX) XXX-XXXX format
- [ ] Pattern uses \d for digits and \( \) for literal parentheses
- [ ] Test-PhoneNumber returns true for valid numbers
- [ ] Test-PhoneNumber returns false for invalid formats
- [ ] Email regex validates user@domain.tld format
- [ ] Pattern handles dots, underscores in usernames
- [ ] Test-EmailAddress returns true for valid emails
- [ ] Test-EmailAddress returns false for invalid formats
- [ ] Understand capture groups with ()
- [ ] Know how to access $Matches array
- [ ] Understand -match vs -like operators
- [ ] Know common regex metacharacters
