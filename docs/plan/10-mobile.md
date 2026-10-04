# 10 — تطبيق الموبايل (Android + iOS)

الغرض: مواصفات `apps/mobile`. الموبايل واجهة المتابعة والتسليم السريع، مع تكافؤ المشاهدة الحيّة والـ Takeover مع الديسكتوب.

## الحزمة

- Expo بآخر SDK مستقر مع Expo Router (deep links `mexa://`) وEAS Build وSubmit وUpdate.
- حزم المونوريبو: `@mexa/api-client`, `app-core`, `contracts`, `i18n`, `design-tokens`, `ui-native`.
- NativeWind وReanimated 3 وGesture Handler.
- TanStack Query مع persist إلى MMKV (قراءة دون اتصال)، وZustand.
- `expo-secure-store` للتوكنات، و`expo-local-authentication` لقفل بيومتري اختياري.
- `expo-notifications` عبر Expo Push إلى APNs وFCM.
- `expo-image-picker` و`document-picker` و`camera`، و`expo-audio` للإملاء.
- `expo-share-intent` لمشاركة نص وصور وملفات إلى بوت.
- الشاشة الحيّة: WebView لصفحة noVNC (المرحلة 2) ثم `react-native-webrtc` (المرحلة 4).
- Sentry وPostHog.
- الحد الأدنى: Android 9 (API 28) وiOS 16.

## الشاشات

```
(auth)   welcome · sign-in · sign-up · onboarding
(tabs)
  bots/            قائمة البوتات وحالتها
    [botId]/chat   بث، مرفقات، إملاء، خطوات، مسودات للموافقة
      memory · routines (عرض/تفعيل) · settings
  computer/        الحالة، تشغيل/إيقاف، الشاشة الحيّة، الملفات
    screen         عرض كامل + Takeover + إعادة التحكم
  inbox/           الإشعارات والموافقات المعلّقة
  settings/        الحساب، اللغة والأرقام، الإشعارات، الأمان، الاشتراك، حذف الحساب
(modals) new-bot · new-group · attach · approval/[id] · share-target
```

## التدفقات

1. **إشعار ثم موافقة:** Push `approval.requested` يفتح `approval/[id]` مباشرة، مع أزرار إجراء في الإشعار.
2. **لقطة شاشة إلى بوت:** مشاركة من أي تطبيق، اختيار البوت، رفع وإرسال.
3. **Takeover:** إشعار "البوت يحتاج كلمة مرور/MFA"، فتح الشاشة، تدخّل، ثم إعادة التحكم.
4. **دون اتصال:** قراءة آخر المحادثات من الكاش، وتُصفّ الرسائل وتُرسل بـ idempotency عند عودة الشبكة.

## العربية وRTL

- `I18nManager.forceRTL` حسب اللغة مع إعادة تحميل، وأنماط `start/end` فقط.
- IBM Plex Sans Arabic وInter عبر `expo-font`.
- أرقام عربية/هندية اختيارية عبر `Intl.NumberFormat('ar-EG-u-nu-arab')`.
- اختبار لقطات لكل شاشة بالاتجاهين.

## الأمان على الجهاز

التوكنات في Keychain/Keystore، وقفل بيومتري قبل الموافقات الحسّاسة، وحجب لقطات الشاشة في شاشة الـ Takeover (`FLAG_SECURE`) اختيارياً، وcertificate pinning للخطة الفريقية.

## متطلبات المتاجر

- **iOS:** Sign in with Apple مع Google، حذف الحساب داخل التطبيق، Privacy Manifest، IAP عبر RevenueCat، ووصف استخدام الميكروفون والكاميرا.
- **Android:** Play Billing عبر RevenueCat، Data safety form، وأحدث target SDK.
- سياسة الخصوصية والشروط على `apps/site`.

## التوزيع

| القناة | الأداة |
|---|---|
| تطوير | `eas build --profile development` مع Dev Client |
| داخلي | TestFlight وPlay Internal عبر `eas submit` |
| Beta | TestFlight External وPlay Closed Testing |
| إنتاج | `eas submit --profile production`، وإصلاحات JS عبر `eas update` |

`eas.json` فيه `development` و`preview` و`production`. `app.config.ts` يقرأ `APP_ENV` لتبديل bundle id (`com.mexa.bots.dev` أو `com.mexa.bots`) والأيقونة وعنوان الـ API.

## الأداء

فتح بارد أقل من ثانيتين، وTTFT أقل من ثانيتين، قوائم بـ FlashList، صور بـ expo-image، وتجميع deltas كل 50ms أثناء البث.

## قرارات مفتوحة

- رفع الحد الأدنى إلى iOS 17: **التوصية** عند الإطلاق إن قلّت نسبة iOS 16 عن 5%.
- تحرير Routines على الموبايل: **التوصية** v2.
