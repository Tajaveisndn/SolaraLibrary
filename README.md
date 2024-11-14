-- Solara Library v1.0
local Solara = {}
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local CoreGui = game:GetService("CoreGui")

-- Configurações de cores e estilo
local theme = {
    Background = Color3.fromRGB(25, 25, 25),
    Accent = Color3.fromRGB(60, 145, 230),
    Secondary = Color3.fromRGB(30, 30, 30),
    TextColor = Color3.fromRGB(255, 255, 255),
    ElementBackground = Color3.fromRGB(35, 35, 35),
    ElementBackgroundHover = Color3.fromRGB(40, 40, 40)
}

-- Configurações de animação
local function CreateTween(instance, properties, duration)
    return TweenService:Create(
        instance,
        TweenInfo.new(duration or 0.3, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),
        properties
    )
end

function Solara.CreateLib(title, themeOverride)
    if themeOverride then
        for key, value in pairs(themeOverride) do
            theme[key] = value
        end
    end

    -- Criar ScreenGui principal
    local SolaraGui = Instance.new("ScreenGui")
    SolaraGui.Name = "SolaraLib"
    SolaraGui.Parent = CoreGui
    SolaraGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

    -- Container principal
    local MainFrame = Instance.new("Frame")
    MainFrame.Name = "MainFrame"
    MainFrame.Size = UDim2.new(0, 600, 0, 400)
    MainFrame.Position = UDim2.new(0.5, -300, 0.5, -200)
    MainFrame.BackgroundColor3 = theme.Background
    MainFrame.BorderSizePixel = 0
    MainFrame.Parent = SolaraGui

    -- Arredondamento das bordas
    local UICorner = Instance.new("UICorner")
    UICorner.CornerRadius = UDim.new(0, 6)
    UICorner.Parent = MainFrame

    -- Sombra
    local DropShadow = Instance.new("ImageLabel")
    DropShadow.Name = "DropShadow"
    DropShadow.AnchorPoint = Vector2.new(0.5, 0.5)
    DropShadow.BackgroundTransparency = 1
    DropShadow.Position = UDim2.new(0.5, 0, 0.5, 0)
    DropShadow.Size = UDim2.new(1, 47, 1, 47)
    DropShadow.ZIndex = -1
    DropShadow.Image = "rbxassetid://6015897843"
    DropShadow.ImageTransparency = 0.5
    DropShadow.ImageColor3 = Color3.new(0, 0, 0)
    DropShadow.Parent = MainFrame

    -- Barra de título
    local TitleBar = Instance.new("Frame")
    TitleBar.Name = "TitleBar"
    TitleBar.Size = UDim2.new(1, 0, 0, 30)
    TitleBar.BackgroundColor3 = theme.Secondary
    TitleBar.BorderSizePixel = 0
    TitleBar.Parent = MainFrame

    local TitleBarCorner = Instance.new("UICorner")
    TitleBarCorner.CornerRadius = UDim.new(0, 6)
    TitleBarCorner.Parent = TitleBar

    -- Texto do título
    local TitleText = Instance.new("TextLabel")
    TitleText.Name = "Title"
    TitleText.Text = title or "Solara Library"
    TitleText.Size = UDim2.new(1, -10, 1, 0)
    TitleText.Position = UDim2.new(0, 10, 0, 0)
    TitleText.BackgroundTransparency = 1
    TitleText.TextColor3 = theme.TextColor
    TitleText.TextSize = 16
    TitleText.Font = Enum.Font.GothamBold
    TitleText.TextXAlignment = Enum.TextXAlignment.Left
    TitleText.Parent = TitleBar

    -- Container de Tabs
    local TabContainer = Instance.new("Frame")
    TabContainer.Name = "TabContainer"
    TabContainer.Size = UDim2.new(0, 130, 1, -35)
    TabContainer.Position = UDim2.new(0, 5, 0, 35)
    TabContainer.BackgroundColor3 = theme.Secondary
    TabContainer.BorderSizePixel = 0
    TabContainer.Parent = MainFrame

    local TabContainerCorner = Instance.new("UICorner")
    TabContainerCorner.CornerRadius = UDim.new(0, 6)
    TabContainerCorner.Parent = TabContainer

    -- Container de conteúdo
    local ContentContainer = Instance.new("Frame")
    ContentContainer.Name = "ContentContainer"
    ContentContainer.Size = UDim2.new(1, -145, 1, -35)
    ContentContainer.Position = UDim2.new(0, 140, 0, 35)
    ContentContainer.BackgroundColor3 = theme.Secondary
    ContentContainer.BorderSizePixel = 0
    ContentContainer.Parent = MainFrame

    local ContentContainerCorner = Instance.new("UICorner")
    ContentContainerCorner.CornerRadius = UDim.new(0, 6)
    ContentContainerCorner.Parent = ContentContainer

    -- Sistema de arrastar janela
    local dragging
    local dragInput
    local dragStart
    local startPos

    local function update(input)
        local delta = input.Position - dragStart
        MainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end

    TitleBar.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            dragStart = input.Position
            startPos = MainFrame.Position
        end
    end)

    TitleBar.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = false
        end
    end)

    UserInputService.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement then
            dragInput = input
        end
    end)

    UserInputService.InputChanged:Connect(function(input)
        if input == dragInput and dragging then
            update(input)
        end
    end)

    -- API para criar tabs e elementos
    local Library = {}

    function Library:CreateTab(name)
        local Tab = {}
        
        -- Botão do tab
        local TabButton = Instance.new("TextButton")
        TabButton.Name = name.."Tab"
        TabButton.Size = UDim2.new(1, -10, 0, 30)
        TabButton.Position = UDim2.new(0, 5, 0, #TabContainer:GetChildren() * 35)
        TabButton.BackgroundColor3 = theme.ElementBackground
        TabButton.Text = name
        TabButton.TextColor3 = theme.TextColor
        TabButton.TextSize = 14
        TabButton.Font = Enum.Font.GothamSemibold
        TabButton.Parent = TabContainer
        TabButton.AutoButtonColor = false

        local TabButtonCorner = Instance.new("UICorner")
        TabButtonCorner.CornerRadius = UDim.new(0, 4)
        TabButtonCorner.Parent = TabButton

        -- Conteúdo do tab
        local TabContent = Instance.new("ScrollingFrame")
        TabContent.Name = name.."Content"
        TabContent.Size = UDim2.new(1, -10, 1, -10)
        TabContent.Position = UDim2.new(0, 5, 0, 5)
        TabContent.BackgroundTransparency = 1
        TabContent.ScrollBarThickness = 3
        TabContent.Visible = false
        TabContent.Parent = ContentContainer
        
        local UIListLayout = Instance.new("UIListLayout")
        UIListLayout.Padding = UDim.new(0, 5)
        UIListLayout.Parent = TabContent
        
        -- Hover effect
        TabButton.MouseEnter:Connect(function()
            CreateTween(TabButton, {BackgroundColor3 = theme.ElementBackgroundHover}):Play()
        end)
        
        TabButton.MouseLeave:Connect(function()
            CreateTween(TabButton, {BackgroundColor3 = theme.ElementBackground}):Play()
        end)
        
        -- Click handling
        TabButton.MouseButton1Click:Connect(function()
            for _, child in pairs(ContentContainer:GetChildren()) do
                if child:IsA("ScrollingFrame") then
                    child.Visible = false
                end
            end
            TabContent.Visible = true
        end)

        function Tab:AddButton(text, callback)
            local Button = Instance.new("TextButton")
            Button.Name = text.."Button"
            Button.Size = UDim2.new(1, 0, 0, 30)
            Button.BackgroundColor3 = theme.ElementBackground
            Button.Text = text
            Button.TextColor3 = theme.TextColor
            Button.TextSize = 14
            Button.Font = Enum.Font.Gotham
            Button.Parent = TabContent
            Button.AutoButtonColor = false

            local ButtonCorner = Instance.new("UICorner")
            ButtonCorner.CornerRadius = UDim.new(0, 4)
            ButtonCorner.Parent = Button

            Button.MouseEnter:Connect(function()
                CreateTween(Button, {BackgroundColor3 = theme.ElementBackgroundHover}):Play()
            end)

            Button.MouseLeave:Connect(function()
                CreateTween(Button, {BackgroundColor3 = theme.ElementBackground}):Play()
            end)

            Button.MouseButton1Click:Connect(function()
                callback()
            end)
        end

        function Tab:AddToggle(text, default, callback)
            local ToggleContainer = Instance.new("Frame")
            ToggleContainer.Name = text.."Toggle"
            ToggleContainer.Size = UDim2.new(1, 0, 0, 30)
            ToggleContainer.BackgroundColor3 = theme.ElementBackground
            ToggleContainer.Parent = TabContent

            local ToggleCorner = Instance.new("UICorner")
            ToggleCorner.CornerRadius = UDim.new(0, 4)
            ToggleCorner.Parent = ToggleContainer

            local ToggleText = Instance.new("TextLabel")
            ToggleText.Name = "Text"
            ToggleText.Text = text
            ToggleText.Size = UDim2.new(1, -50, 1, 0)
            ToggleText.Position = UDim2.new(0, 10, 0, 0)
            ToggleText.BackgroundTransparency = 1
            ToggleText.TextColor3 = theme.TextColor
            ToggleText.TextSize = 14
            ToggleText.Font = Enum.Font.Gotham
            ToggleText.TextXAlignment = Enum.TextXAlignment.Left
            ToggleText.Parent = ToggleContainer

            local ToggleButton = Instance.new("TextButton")
            ToggleButton.Name = "ToggleButton"
            ToggleButton.Size = UDim2.new(0, 40, 0, 20)
            ToggleButton.Position = UDim2.new(1, -45, 0.5, -10)
            ToggleButton.BackgroundColor3 = theme.ElementBackground
            ToggleButton.Text = ""
            ToggleButton.Parent = ToggleContainer
            ToggleButton.AutoButtonColor = false

            local ToggleButtonCorner = Instance.new("UICorner")
            ToggleButtonCorner.CornerRadius = UDim.new(1, 0)
            ToggleButtonCorner.Parent = ToggleButton

            local ToggleCircle = Instance.new("Frame")
            ToggleCircle.Name = "Circle"
            ToggleCircle.Size = UDim2.new(0, 16, 0, 16)
            ToggleCircle.Position = UDim2.new(0, 2, 0.5, -8)
            ToggleCircle.BackgroundColor3 = theme.TextColor
            ToggleCircle.Parent = ToggleButton

            local ToggleCircleCorner = Instance.new("UICorner")
            ToggleCircleCorner.CornerRadius = UDim.new(1, 0)
            ToggleCircleCorner.Parent = ToggleCircle

            local toggled = default or false

            local function updateToggle()
                if toggled then
                    CreateTween(ToggleButton, {BackgroundColor3 = theme.Accent}):Play()
                    CreateTween(ToggleCircle, {Position = UDim2.new(1, -18, 0.5, -8)}):Play()
                else
                    CreateTween(ToggleButton, {BackgroundColor3 = theme.ElementBackground}):Play()
                    CreateTween(ToggleCircle, {Position = UDim2.new(0, 2, 0.5, -8)}):Play()
                end
                callback(toggled)
            end

            ToggleButton.MouseButton1Click:Connect(function()
                toggled = not toggled
                updateToggle()
            end)

            updateToggle()
        end

        -- Mostrar o primeiro tab por padrão
        if #TabContainer:GetChildren() == 1 then
            TabContent.Visible = true
        end

        return Tab
    end

    return Library
end

return Solara
