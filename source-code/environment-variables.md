# Environment Variables

## Environment Variables

The project source code requires certain envrionment variables to be pre-set in order to work as expected. You can get a copy of what the `.env` file should look like from the `.env.example` file in the root of the project's [source code](https://github.com/lazy-media/Reactive-Resume/blob/main/.env.example).

Here, I'll be explaining what each of the environment variables are for, and which ones are actually **required** and which ones aren't.

### App

#### `TZ`

**Required**: `no`\
**Default Value:** `UTC`\
**Description:** Server Timezone

This field is just to indicate the timezone that the server should follow. This is just so that the date time information should be unified and all timestamps should follow the same suit.

#### `SECRET_KEY`

**Required**: `yes`\
**Description:** Secret Key for Client-Server Communication

The secret key can be a unique key, a randomly generated string that is used for client-server communication. You can use this [random.org](https://www.random.org/strings/?num=10\&len=20\&digits=on\&upperalpha=on\&loweralpha=on\&unique=on\&format=html\&rnd=new) configuration to generate a long unique string.

### URLs

#### `PUBLIC_URL`

**Required**: `yes`\
**Description:** URL through which app is accessible

**Default Value:** `http://localhost`

This URL would be used in features like link sharing functionality and authentication redirection. This points only to the client app, as the server would be running on `PORT 3100` always.

#### `PUBLIC_SERVER_URL`

**Required**: `yes`\
**Description:** URL through which server is accessible

**Default Value:** `http://localhost/api`

This URL is used when export PDF functionality is used within the app and has to reach out to the server.

### Database

#### `POSTGRES_HOST`

**Required**: `yes`\
**Default Value:** `localhost`\
**Description:** Hostname for the PostgreSQL Server

#### `POSTGRES_PORT`

**Required**: `yes`\
**Default Value:** `5432`\
**Description:** Port of the PostgreSQL Server

#### `POSTGRES_DB`

**Required**: `yes`\
**Default Value:** `postgres`\
**Description:** Name of the Database in PostgreSQL Server

#### `POSTGRES_USER`

**Required**: `yes`\
**Default Value:** `postgres`\
**Description:** Username of the PostgreSQL Server

#### `POSTGRES_PASSWORD`

**Required**: `yes`\
**Default Value:** `postgres`\
**Description:** Password of the PostgreSQL Server

#### `POSTGRES_SSL_CERT`

**Required**: `no`\
**Description:** Base64 Encoded String of the SSL CA Certificate

Some production-grade managed databases require the need to pass a CA certificate along with the options. You can encode the contents of the `.crt` file sent to you by your cloud provider in _Base64_ and provide it as a environment variable.

### Authentication

#### `JWT_SECRET`

**Required**: `yes`\
**Description:** Secret to Sign and Extract JWT Payloads

Similar to the `SECRET_KEY`, this can also be a unique generated string. This is used for email/password authentication, to hash + salt passwords stored in the database so they are unreadable.

#### `JWT_EXPIRY_TIME`

**Required**: `yes`\
**Default Value:** `604800`\
**Description:** How long should the JWT be valid for?

This value, in milliseconds, denotes the validity of the JWT token. A shorter value would make the user have to login more frequently.

### Google

As much as I'd like to keep Google out of the picture, they do provide a few services that are actually useful. Namely, Google OAuth (for Login with Google) and Google Fonts (to list all fonts and use them on a resume).

#### `PUBLIC_GOOGLE_CLIENT_ID`

**Required**: `no`\
**Description:** Google Client ID for Google Login

This field is only required if the Google Login functionality is important to you.

#### `GOOGLE_CLIENT_SECRET`

**Required**: `no`\
**Description:** Google Client Secret for Google Login

This field is only required if the Google Login functionality is important to you.

#### `GOOGLE_API_KEY`

**Required**: `no`\
**Description:** Google API Key used for fetching Google Fonts

Within the resume builder, there's a section where you can pick any font from the Google Fonts Library. To fetch the names and IDs of these fonts, we depend on the Google Fonts API. It does not cost any payment, or the need to enter credit card information to create or use this API.

You can get your own key here: https://developers.google.com/fonts/docs/developer\_api

If you do not have a Google API Key, it was make use of the cached response JSON that's stored within the project source. Please note that this cache is not updated and may not have all the latest fonts that Google Fonts has to offer.

### Mail

The server makes use of SMTP to send the password reset email to those who have forgotten their password. **This section is completely optional for those who do not require this functionality.**

#### `MAIL_FROM_NAME`

**Required**: `no`\
**Description:** Sender's Name

#### `MAIL_FROM_EMAIL`

**Required**: `no`\
**Description:** Sender's Email Address

### Storage

You can either use S3 or any S3-compliant service such as DigitalOcean Spaces to store profile pictures uploaded by users of the platform.

#### `STORAGE_BUCKET`

**Required**: `yes`

#### `STORAGE_REGION`

**Required**: `yes`

#### `STORAGE_ENDPOINT`

**Required**: `yes`

#### `STORAGE_URL_PREFIX`

**Required**: `yes`

#### `STORAGE_ACCESS_KEY`

**Required**: `yes`

#### `STORAGE_SECRET_KEY`

**Required**: `yes`

### Cache

These are just some settings to help ease the repetitive requests made to the server.

#### `PDF_DELETION_TIME`

**Required**: `no`
**Default Value**: `345600000 ms` / `4 days`
**Description:** Determines when the PDF should be deleted from the server, in case the user tries to download it again (in ms)
