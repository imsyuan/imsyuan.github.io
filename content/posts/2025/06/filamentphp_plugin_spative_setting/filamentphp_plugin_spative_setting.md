+++
date = '2025-06-17T20:00:29+08:00'
draft = false
title = '如何在 FilamentPHP 中使用 Spatie Setting 插件'
description = ''
author = 'Steven Chang'
slug = 'filamentphp_plugin_spative_setting'
archive = ['2025']
tags = ['filamentphp', 'spatie', 'setting', 'laravel', 'plugin']
categories = ['filamentphp', 'laravel']
cover = { image = '/posts/2025/06/filamentphp_plugin_spative_setting/cover.png', alt = 'Cover Image'}
+++

在 Filamentphp 開發過程中，使用 Spatie Setting 插件，一開始一直卡關，所以寫這篇文章記錄一下。

## 安裝 Spatie Setting 插件
[插件 filament-spatie-settings](https://filamentphp.com/plugins/filament-spatie-settings)

### 安裝 spatie-laravel-settings-plugin
```bash
composer require filament/spatie-laravel-settings-plugin:"^3.2" -W
```

### 安裝相關 migration 和 class
用 `spatie/laravel-settings` 這個套件，所以需要根據 [installation](https://github.com/spatie/laravel-settings?tab=readme-ov-file#installation) 來安裝相關 migration 和 class

> 安裝 migration

```bash
php artisan vendor:publish --provider="Spatie\LaravelSettings\LaravelSettingsServiceProvider" --tag="migrations"
php artisan migrate
```

你會取得 migration 如下，並且執行 migrate

```php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up()
    {
        Schema::create('settings', function (Blueprint $table): void {
            $table->id();

            $table->string('group');
            $table->string('name');
            $table->boolean('locked')->default(false);
            $table->json('payload');

            $table->timestamps();

            $table->unique(['group', 'name']);
        });
    }
};
```

> 安裝 class

publish setting 的 class，位置 `app/Settings`

```bash
php artisan vendor:publish --provider="Spatie\LaravelSettings\LaravelSettingsServiceProvider" --tag="config"
```

> 製作 setting class

```bash
# php artisan make:setting SettingName --group=groupName 
php artisan make:filament-settings-page ManageGeneras GeneralSettings
```

這樣就會在 `app/Settings` 目錄下產生一個 `GeneralSettings.php` 的檔案。

```php
use Spatie\LaravelSettings\Settings;

class GeneralSettings extends Settings
{
    public string $site_name;
    public bool $site_active;
    
    public static function group(): string
    {
        return 'general';
    }
}
```

> 新增你的 setting class 到你的 `/config/settings.php`

```php
    /*
     * Each settings class used in your application must be registered, you can
     * add them (manually) here.
     */
    'settings' => [
        GeneralSettings::class
    ],
```
### 產生需要的 properties

```bash
php artisan make:settings-migration CreateGeneralSettings
```

這樣會在 `database/database/settings` 目錄下產生一個 `...create_general_settings.php` 的檔案。

```php
use Spatie\LaravelSettings\Migrations\SettingsMigration;

return new class extends SettingsMigration
{
    public function up(): void
    {
        $this->migrator->add('general.site_name', 'Spatie');
        $this->migrator->add('general.site_active', true);
    }
}
```

```bash
php artisan migrate
```

這樣就完成 `spatie/laravel-settings` 基礎設定

### 準備 filament setting Resource

```bash
php artisan make:filament-settings-page ManageGeneras GeneralSettings 
```

這樣會在 `app/Filament/Pages` 下產生一個 `ManageGenerals.php`，確認 $settings 是不是剛剛的 Setting class

```php
protected static string $settings = GeneralSettings::class;
```

輸入在自己的 Resource 裡面，然後在 `form()` 方法裡面新增欄位。

```php
use Filament\Forms\Components\Repeater;
use Filament\Forms\Components\TextInput;
use Filament\Forms\Form;
 
public function form(Form $form): Form
{
    return $form
        ->schema([
            TextInput::make('copyright')
                ->label('Copyright notice')
                ->required(),
            Repeater::make('links')
                ->schema([
                    TextInput::make('label')->required(),
                    TextInput::make('url')
                        ->url()
                        ->required(),
                ]),
        ]);
}
```
> 結果如下

![result.png](/posts/2025/06/filamentphp_plugin_spative_setting/result.png)