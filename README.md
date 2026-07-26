# İSGCep — Tüm Modüller Birleştirilmiş
# Bu dosya kurulum rehberi olarak kullanılır.
# Her bölümü ilgili dosya yoluna kopyala.

# ============================================================
# DOSYA: lib/core/constants/app_colors.dart
# ============================================================

/*
import 'package:flutter/material.dart';

class AppColors {
  AppColors._();

  static const Color safetyYellow  = Color(0xFFFFB800);
  static const Color industrialNav = Color(0xFF1A2B4A);
  static const Color safetyGreen   = Color(0xFF2ECC71);
  static const Color hazardRed     = Color(0xFFE74C3C);
  static const Color alertOrange   = Color(0xFFE67E22);

  static const Color lightBackground   = Color(0xFFF8F9FA);
  static const Color lightSurface      = Color(0xFFFFFFFF);
  static const Color lightSurfaceVar   = Color(0xFFF0F4F8);
  static const Color lightOnBackground = Color(0xFF1A2B4A);
  static const Color lightOnSurface    = Color(0xFF2C3E50);
  static const Color lightSubtle       = Color(0xFF7F8C8D);
  static const Color lightDivider      = Color(0xFFE0E6ED);

  static const Color darkBackground    = Color(0xFF0F1923);
  static const Color darkSurface       = Color(0xFF1A2535);
  static const Color darkSurfaceVar    = Color(0xFF243040);
  static const Color darkOnBackground  = Color(0xFFE8EDF2);
  static const Color darkOnSurface     = Color(0xFFCBD5DF);
  static const Color darkSubtle        = Color(0xFF7A8FA6);
  static const Color darkDivider       = Color(0xFF243040);
}
*/

# ============================================================
# DOSYA: lib/core/constants/app_constants.dart
# ============================================================

/*
class AppConstants {
  static const String revenueCatAppleKey  = 'appl_XXXXXXXXXXXX';
  static const String revenueCatGoogleKey = 'goog_XXXXXXXXXXXX';
  static const String premiumEntitlementId  = 'isg_premium';
  static const String monthlyProductId      = 'isg_premium_monthly';
  static const String yearlyProductId       = 'isg_premium_yearly';
  static const int freeQuestionLimit = 20;
}
*/

# ============================================================
# DOSYA: lib/core/utils/responsive.dart
# ============================================================

/*
import 'package:flutter/material.dart';

enum ScreenSize { mobile, tablet, desktop }

class Responsive {
  static const double mobileMax = 600;
  static const double tabletMax = 1024;

  static ScreenSize of(BuildContext context) {
    final width = MediaQuery.of(context).size.width;
    if (width < mobileMax) return ScreenSize.mobile;
    if (width < tabletMax) return ScreenSize.tablet;
    return ScreenSize.desktop;
  }

  static bool isMobile(BuildContext context) => of(context) == ScreenSize.mobile;
  static bool isTablet(BuildContext context) => of(context) == ScreenSize.tablet;
  static bool isDesktop(BuildContext context) => of(context) == ScreenSize.desktop;

  static double contentWidth(BuildContext context) {
    final width = MediaQuery.of(context).size.width;
    if (width >= tabletMax) return 800;
    if (width >= mobileMax) return width * 0.85;
    return width;
  }

  static int gridColumns(BuildContext context) {
    switch (of(context)) {
      case ScreenSize.mobile:  return 1;
      case ScreenSize.tablet:  return 2;
      case ScreenSize.desktop: return 3;
    }
  }
}
*/

# ============================================================
# DOSYA: lib/core/widgets/responsive_layout.dart
# ============================================================

/*
import 'package:flutter/material.dart';
import 'responsive.dart';

class ResponsiveLayout extends StatelessWidget {
  final Widget child;
  final Color? backgroundColor;

  const ResponsiveLayout({super.key, required this.child, this.backgroundColor});

  @override
  Widget build(BuildContext context) {
    final isMobile = Responsive.isMobile(context);
    if (isMobile) return child;

    return Scaffold(
      backgroundColor: backgroundColor ?? Theme.of(context).scaffoldBackgroundColor,
      body: Center(
        child: ConstrainedBox(
          constraints: const BoxConstraints(maxWidth: 800),
          child: Card(
            elevation: 4,
            margin: const EdgeInsets.symmetric(vertical: 24),
            clipBehavior: Clip.antiAlias,
            child: child,
          ),
        ),
      ),
    );
  }
}
*/

# ============================================================
# DOSYA: lib/services/theme_service.dart
# ============================================================

/*
import 'package:flutter/material.dart';
import '../data/local/hive/hive_boxes.dart';

class ThemeService extends ChangeNotifier {
  static const _key = 'theme_mode';

  ThemeMode get themeMode {
    final stored = HiveBoxes.settingsBox.get(_key, defaultValue: 'system');
    switch (stored) {
      case 'light':  return ThemeMode.light;
      case 'dark':   return ThemeMode.dark;
      default:       return ThemeMode.system;
    }
  }

  Future<void> setThemeMode(ThemeMode mode) async {
    String value;
    switch (mode) {
      case ThemeMode.light:  value = 'light'; break;
      case ThemeMode.dark:   value = 'dark';  break;
      case ThemeMode.system: value = 'system'; break;
    }
    await HiveBoxes.settingsBox.put(_key, value);
    notifyListeners();
  }
}
*/

# ============================================================
# DOSYA: lib/services/revenue_cat_service.dart
# ============================================================

/*
import 'package:flutter/foundation.dart';
import 'package:purchases_flutter/purchases_flutter.dart';
import '../core/constants/app_constants.dart';

class RevenueCatService {
  static CustomerInfo? _customerInfo;

  static Future<void> init(String userId) async {
    if (kIsWeb) return;
    await Purchases.setLogLevel(LogLevel.debug);
    final config = PurchasesConfiguration(
      defaultTargetPlatform == TargetPlatform.iOS
          ? AppConstants.revenueCatAppleKey
          : AppConstants.revenueCatGoogleKey,
    )..appUserID = userId;
    await Purchases.configure(config);
    _customerInfo = await Purchases.getCustomerInfo();
  }

  static bool get isPremium {
    if (kIsWeb) return false;
    return _customerInfo?.entitlements.active
        .containsKey(AppConstants.premiumEntitlementId) ?? false;
  }

  static Future<Offerings?> getOfferings() async {
    if (kIsWeb) return null;
    try { return await Purchases.getOfferings(); } catch (e) { return null; }
  }

  static Future<PurchaseResult> purchase(Package package) async {
    try {
      final info = await Purchases.purchasePackage(package);
      _customerInfo = info;
      return PurchaseResult(success: true, customerInfo: info);
    } on PurchasesErrorCode catch (e) {
      if (e == PurchasesErrorCode.purchaseCancelledError) {
        return PurchaseResult(success: false, cancelled: true);
      }
      return PurchaseResult(success: false, error: e.toString());
    }
  }

  static Future<PurchaseResult> restore() async {
    if (kIsWeb) return PurchaseResult(success: false);
    try {
      final info = await Purchases.restorePurchases();
      _customerInfo = info;
      return PurchaseResult(success: true, customerInfo: info);
    } catch (e) {
      return PurchaseResult(success: false, error: e.toString());
    }
  }

  static Future<void> refresh() async {
    if (kIsWeb) return;
    try { _customerInfo = await Purchases.getCustomerInfo(); } catch (_) {}
  }

  static Future<void> syncAfterPurchase(String userId) async {
    await Future.delayed(const Duration(seconds: 2));
    final profile = await Supabase.instance.client
        .from('user_profiles')
        .select('is_premium, premium_expires_at')
        .eq('id', userId)
        .single();
    debugPrint('Premium sync: ${profile['is_premium']}');
  }
}

class PurchaseResult {
  final bool success;
  final bool cancelled;
  final CustomerInfo? customerInfo;
  final String? error;

  PurchaseResult({
    required this.success,
    this.cancelled = false,
    this.customerInfo,
    this.error,
  });
}
*/

# ============================================================
# DOSYA: lib/models/auth_model.dart
# ============================================================

/*
class AuthUserModel {
  final String id;
  final String email;
  final bool emailConfirmed;

  AuthUserModel({
    required this.id,
    required this.email,
    required this.emailConfirmed,
  });

  factory AuthUserModel.fromSupabase(dynamic user) {
    return AuthUserModel(
      id: user.id,
      email: user.email ?? '',
      emailConfirmed: user.emailConfirmedAt != null,
    );
  }
}
*/

# ============================================================
# DOSYA: lib/data/repositories/auth_repository.dart
# ============================================================

/*
import 'package:supabase_flutter/supabase_flutter.dart';
import '../../models/auth_model.dart';

class AuthRepository {
  final SupabaseClient _client = Supabase.instance.client;

  AuthUserModel? get currentUser {
    final user = _client.auth.currentUser;
    if (user == null) return null;
    return AuthUserModel.fromSupabase(user);
  }

  Stream<AuthState> get authStateChanges => _client.auth.onAuthStateChange;

  Future<AuthUserModel> signUp({required String email, required String password}) async {
    final response = await _client.auth.signUp(email: email, password: password);
    if (response.user == null) throw Exception('Kayıt başarısız');
    await _client.from('user_profiles').upsert({
      'id': response.user!.id,
      'target_certificate_class': 'C',
    });
    return AuthUserModel.fromSupabase(response.user!);
  }

  Future<AuthUserModel> signIn({required String email, required String password}) async {
    final response = await _client.auth.signInWithPassword(email: email, password: password);
    if (response.user == null) throw Exception('Giriş başarısız');
    return AuthUserModel.fromSupabase(response.user!);
  }

  Future<void> signInWithGoogle() async {
    await _client.auth.signInWithOAuth(
      OAuthProvider.google,
      redirectTo: 'https://isgcep-ux.github.io/isgcep/',
    );
  }

  Future<void> resetPassword(String email) async {
    await _client.auth.resetPasswordForEmail(
      email,
      redirectTo: 'https://isgcep-ux.github.io/isgcep/reset-password',
    );
  }

  Future<void> signOut() async => await _client.auth.signOut();
}
*/

# ============================================================
# DOSYA: lib/features/auth/bloc/auth_event.dart
# ============================================================

/*
import 'package:equatable/equatable.dart';

abstract class AuthEvent extends Equatable {
  const AuthEvent();
  @override
  List<Object?> get props => [];
}

class AuthStarted extends AuthEvent { const AuthStarted(); }

class AuthSignInRequested extends AuthEvent {
  final String email;
  final String password;
  const AuthSignInRequested({required this.email, required this.password});
  @override
  List<Object?> get props => [email, password];
}

class AuthSignUpRequested extends AuthEvent {
  final String email;
  final String password;
  const AuthSignUpRequested({required this.email, required this.password});
  @override
  List<Object?> get props => [email, password];
}

class AuthGoogleSignInRequested extends AuthEvent { const AuthGoogleSignInRequested(); }

class AuthPasswordResetRequested extends AuthEvent {
  final String email;
  const AuthPasswordResetRequested(this.email);
  @override
  List<Object?> get props => [email];
}

class AuthSignOutRequested extends AuthEvent { const AuthSignOutRequested(); }
*/

# ============================================================
# DOSYA: lib/features/auth/bloc/auth_state.dart
# ============================================================

/*
import 'package:equatable/equatable.dart';
import '../../../models/auth_model.dart';

enum AuthStatus {
  initial, loading, authenticated, unauthenticated,
  emailConfirmationPending, passwordResetSent, error,
}

class AuthState extends Equatable {
  final AuthStatus status;
  final AuthUserModel? user;
  final String? errorMessage;

  const AuthState({this.status = AuthStatus.initial, this.user, this.errorMessage});

  bool get isAuthenticated => status == AuthStatus.authenticated;

  AuthState copyWith({AuthStatus? status, AuthUserModel? user, String? errorMessage}) {
    return AuthState(
      status: status ?? this.status,
      user: user ?? this.user,
      errorMessage: errorMessage,
    );
  }

  @override
  List<Object?> get props => [status, user, errorMessage];
}
*/

# ============================================================
# DOSYA: lib/features/premium/bloc/premium_event.dart
# ============================================================

/*
import 'package:equatable/equatable.dart';
import 'package:purchases_flutter/purchases_flutter.dart';

abstract class PremiumEvent extends Equatable {
  const PremiumEvent();
  @override
  List<Object?> get props => [];
}

class PremiumLoadRequested extends PremiumEvent { const PremiumLoadRequested(); }

class PremiumPurchaseRequested extends PremiumEvent {
  final Package package;
  const PremiumPurchaseRequested(this.package);
  @override
  List<Object?> get props => [package];
}

class PremiumRestoreRequested extends PremiumEvent { const PremiumRestoreRequested(); }
*/

# ============================================================
# DOSYA: lib/features/premium/bloc/premium_state.dart
# ============================================================

/*
import 'package:equatable/equatable.dart';
import 'package:purchases_flutter/purchases_flutter.dart';

enum PremiumStatus { initial, loading, loaded, purchasing, success, error }

class PremiumState extends Equatable {
  final PremiumStatus status;
  final bool isPremium;
  final Offerings? offerings;
  final String? errorMessage;
  final bool purchaseCancelled;

  const PremiumState({
    this.status = PremiumStatus.initial,
    this.isPremium = false,
    this.offerings,
    this.errorMessage,
    this.purchaseCancelled = false,
  });

  PremiumState copyWith({
    PremiumStatus? status,
    bool? isPremium,
    Offerings? offerings,
    String? errorMessage,
    bool? purchaseCancelled,
  }) {
    return PremiumState(
      status: status ?? this.status,
      isPremium: isPremium ?? this.isPremium,
      offerings: offerings ?? this.offerings,
      errorMessage: errorMessage,
      purchaseCancelled: purchaseCancelled ?? false,
    );
  }

  @override
  List<Object?> get props => [status, isPremium, offerings, errorMessage, purchaseCancelled];
}
*/

# ============================================================
# DOSYA: supabase/functions/_shared/cors.ts
# ============================================================

/*
export const corsHeaders = {
  'Access-Control-Allow-Origin': '*',
  'Access-Control-Allow-Headers':
    'authorization, x-client-info, apikey, content-type, x-revenuecat-signature',
};
*/

# ============================================================
# DOSYA: web/404.html
# ============================================================

/*
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title>İSGCep</title>
  <script>
    var pathSegmentsToKeep = 1;
    var l = window.location;
    l.replace(
      l.protocol + '//' + l.hostname + (l.port ? ':' + l.port : '') +
      l.pathname.split('/').slice(0, 1 + pathSegmentsToKeep).join('/') +
      '/?/' +
      l.pathname.slice(1).split('/').slice(pathSegmentsToKeep).join('/').replace(/&/g, '~and~') +
      (l.search ? '&' + l.search.slice(1).replace(/&/g, '~and~') : '') +
      l.hash
    );
  </script>
</head>
<body></body>
</html>
*/

# ============================================================
# DOSYA: .github/workflows/flutter-web-deploy.yml
# ============================================================

# (Bu dosyanın içeriği için ayrı indirilen flutter-web-deploy.yml dosyasını kullan)

# ============================================================
# KURULUM SIRASI
# ============================================================
#
# Codespaces terminalinde sırayla çalıştır:
#
# 1. mkdir -p .github/workflows web \
#      lib/core/{constants,theme,utils,widgets} \
#      lib/services lib/models \
#      lib/data/repositories \
#      lib/features/{auth/{bloc,presentation},premium/{bloc,presentation}} \
#      supabase/functions/{revenuecat-webhook,_shared}
#
# 2. Her dosyayı oluştur ve içeriğini yapıştır
#
# 3. flutter pub get
#
# 4. git add .
#    git commit -m "feat: tüm modüller eklendi"
#    git push origin main
#
# ============================================================
# PUBSPEC.YAML EKLENECEKLERİ
# ============================================================
#
# dependencies:
#   flutter_bloc: ^8.1.5
#   equatable: ^2.0.5
#   supabase_flutter: ^2.5.6
#   hive: ^2.2.3
#   hive_flutter: ^1.1.0
#   sqflite: ^2.3.3
#   path_provider: ^2.1.4
#   go_router: ^14.2.0
#   purchases_flutter: ^7.5.0
#   connectivity_plus: ^6.0.3
#   flutter_svg: ^2.0.10+1
#   google_fonts: ^6.2.1
#   provider: ^6.1.2
#   uuid: ^4.4.2
#   cached_network_image: ^3.3.1
#   freezed_annotation: ^2.4.4
#   json_annotation: ^4.9.0
#   intl: ^0.19.0
#
# dev_dependencies:
#   build_runner: ^2.4.11
#   hive_generator: ^2.0.1
#   freezed: ^2.5.5
#   json_serializable: ^6.8.0
#   flutter_lints: ^4.0.0