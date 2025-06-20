‘‘‘@Composable
fun LoginScreen(
    validUsers: List<Pair<String, String>>,
    modifier: Modifier = Modifier
) {
    var username by remember { mutableStateOf("") }
    var password by remember { mutableStateOf("") }
    var message by remember { mutableStateOf<String?>(null) }

    Column(modifier.padding(16.dp)) {
        TextField(
            value = username,
            onValueChange = { username = it },
            label = { Text("Логин") },
            modifier = Modifier
                .fillMaxWidth()
                .testTag("usernameField")
        )
        Spacer(Modifier.height(8.dp))
        TextField(
            value = password,
            onValueChange = { password = it },
            label = { Text("Пароль") },
            visualTransformation = PasswordVisualTransformation(),
            modifier = Modifier
                .fillMaxWidth()
                .testTag("passwordField")
        )
        Spacer(Modifier.height(16.dp))
        Button(
            onClick = {
                message = if (validUsers.contains(username to password)) {
                    "Успешная авторизация"
                } else {
                    "Неверный логин или пароль"
                }
            },
            modifier = Modifier
                .fillMaxWidth()
                .semantics { contentDescription = "loginButton" }
        ) {
            Text("Вход")
        }
        Spacer(Modifier.height(16.dp))
        message?.let {
            Text(it)
        }
    }
} ‘‘‘
Test
import androidx.compose.ui.test.*
import androidx.compose.ui.test.junit4.createComposeRule
import org.junit.Rule
import org.junit.Test

class LoginScreenTest {

    @get:Rule
    val composeTestRule = createComposeRule()

    private val users = listOf("user1" to "pass1")

    @Test
    fun login_successful() {
        composeTestRule.setContent {
            LoginScreen(validUsers = users)
        }

        // ввод валидных данных
        composeTestRule.onNodeWithTag("usernameField")
            .performTextInput("user1")
        composeTestRule.onNodeWithTag("passwordField")
            .performTextInput("pass1")

        // клик по кнопке (семантика)
        composeTestRule.onNodeWithContentDescription("loginButton")
            .performClick()

        // проверка текста
        composeTestRule.onNodeWithText("Успешная авторизация")
            .assertIsDisplayed()
    }

    @Test
    fun login_failure() {
        composeTestRule.setContent {
            LoginScreen(validUsers = users)
        }

        // ввод неверных данных
        composeTestRule.onNodeWithTag("usernameField")
            .performTextInput("wrong")
        composeTestRule.onNodeWithTag("passwordField")
            .performTextInput("creds")

        composeTestRule.onNodeWithContentDescription("loginButton")
            .performClick()

        composeTestRule.onNodeWithText("Неверный логин или пароль")
            .assertIsDisplayed()
    }
}
