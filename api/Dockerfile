# Use the official PHP image with Apache
FROM php:8.2-apache

# Install necessary PHP extensions and git
RUN apt-get update && apt-get install -y \
    libpng-dev \
    libjpeg-dev \
    libfreetype6-dev \
    git \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install gd pdo pdo_mysql

# Enable Apache mod_rewrite and mod_headers
RUN a2enmod rewrite headers

# Install Node.js for Vite build
RUN curl -fsSL https://deb.nodesource.com/setup_18.x | bash - \
    && apt-get install -y nodejs

# Set the working directory
WORKDIR /var/www/html

# Copy the current directory contents into the container at /var/www/html
COPY . .

# Install NPM dependencies (for Vite and frontend assets)
COPY package*.json ./
RUN npm install

# Build assets with Vite
RUN npm run build

# Remove the vendor directory if it exists (this is useful in case of rebuilding from cache)
RUN rm -rf vendor

# Set DocumentRoot to the public folder in Apache configuration
RUN sed -i 's|/var/www/html|/var/www/html/public|g' /etc/apache2/sites-available/000-default.conf

# Install Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Install Laravel dependencies using Composer, optimized for production
RUN composer install --optimize-autoloader --no-dev

# Set Apache ServerName (to avoid errors related to ServerName)
RUN echo "ServerName localhost" >> /etc/apache2/apache2.conf

# Set the permissions for the storage and bootstrap/cache directories
RUN chown -R www-data:www-data storage bootstrap/cache \
    && chmod -R 775 storage bootstrap/cache

# Set the permissions for the public/build directory (where Vite assets are stored)
RUN chown -R www-data:www-data public/build \
    && chmod -R 775 public/build

# Expose the port Apache is running on
EXPOSE 80

# Start the Apache server
CMD ["apache2-foreground"]
