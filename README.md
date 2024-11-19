local SolaraLibrary = {}

local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local CoreGui = game:GetService("CoreGui")
local RunService = game:GetService("RunService")
local TextService = game:GetService("TextService")
local Players = game:GetService("Players")

local function createTween(object, info, properties)
    local tween = TweenService:Create(object, TweenInfo.new(unpack(info)), properties)
    return tween
end

function SolaraLibrary:CreateWindow(config)
    config = config or {}
    local windowName = config.Name or "Solara Library"
    
    -- Main GUI Creation
    local SolaraGUI = Instance.new("ScreenGui")
    SolaraGUI.Name = "SolaraGUI"
    SolaraGUI.Parent = CoreGui
    
    local MainFrame = Instance.new("Frame")
    MainFrame.Name = "MainFrame"
    MainFrame.Size = UDim2.new(0, 400, 0, 500)
    MainFrame.Position = UDim2.new(0.5, -200, 0.5, -250)
    MainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
    MainFrame.BorderSizePixel = 0
    MainFrame.Parent = SolaraGUI
    
    local UICorner = Instance.new("UICorner")
    UICorner.CornerRadius = UDim.new(0, 6)
    UICorner.Parent = MainFrame
    
    -- Profile Section
    local ProfileFrame = Instance.new("Frame")
    ProfileFrame.Name = "ProfileFrame"
    ProfileFrame.Size = UDim2.new(1, 0, 0, 100)
    ProfileFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    ProfileFrame.BorderSizePixel = 0
    ProfileFrame.Parent = MainFrame
    
    local ProfileCorner = Instance.new("UICorner")
    ProfileCorner.CornerRadius = UDim.new(0, 6)
    ProfileCorner.Parent = ProfileFrame
    
    local ProfileImage = Instance.new("ImageLabel")
    ProfileImage.Name = "ProfileImage"
    ProfileImage.Size = UDim2.new(0, 70, 0, 70)
    ProfileImage.Position = UDim2.new(0, 15, 0, 15)
    ProfileImage.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    ProfileImage.Image = Players.LocalPlayer.ProfilePhoto or ""
    ProfileImage.Parent = ProfileFrame
    
    local ProfileImageCorner = Instance.new("UICorner")
    ProfileImageCorner.CornerRadius = UDim.new(1, 0)
    ProfileImageCorner.Parent = ProfileImage
    
    local DisplayName = Instance.new("TextLabel")
    DisplayName.Name = "DisplayName"
    DisplayName.Text = Players.LocalPlayer.DisplayName
    DisplayName.Size = UDim2.new(0, 200, 0, 30)
    DisplayName.Position = UDim2.new(0, 100, 0, 20)
    DisplayName.BackgroundTransparency = 1
    DisplayName.TextColor3 = Color3.fromRGB(255, 255, 255)
    DisplayName.TextSize = 18
    DisplayName.Font = Enum.Font.GothamBold
    DisplayName.TextXAlignment = Enum.TextXAlignment.Left
    DisplayName.Parent = ProfileFrame
    
    local Username = Instance.new("TextLabel")
    Username.Name = "Username"
    Username.Text = "@" .. Players.LocalPlayer.Name
    Username.Size = UDim2.new(0, 200, 0, 20)
    -- Welcome Message
    local WelcomeGui = Instance.new("ScreenGui")
    WelcomeGui.Name = "WelcomeGui"
    WelcomeGui.Parent = CoreGui
    
    local WelcomeFrame = Instance.new("Frame")
    WelcomeFrame.Name = "WelcomeFrame"
    WelcomeFrame.Size = UDim2.new(0, 300, 0, 150)
    WelcomeFrame.Position = UDim2.new(0.5, -150, 0.5, -75)
    WelcomeFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 35)
    WelcomeFrame.BorderSizePixel = 0
    WelcomeFrame.Parent = WelcomeGui
    
    local WelcomeUICorner = Instance.new("UICorner")
    WelcomeUICorner.CornerRadius = UDim.new(0, 8)
    WelcomeUICorner.Parent = WelcomeFrame
    
    local WelcomeTitle = Instance.new("TextLabel")
    WelcomeTitle.Name = "WelcomeTitle"
    WelcomeTitle.Text = "Welcome to " .. windowName
    WelcomeTitle.Size = UDim2.new(1, 0, 0, 40)
    WelcomeTitle.Position = UDim2.new(0, 0, 0.1, 0)
    WelcomeTitle.BackgroundTransparency = 1
    WelcomeTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
    WelcomeTitle.TextSize = 24
    WelcomeTitle.Font = Enum.Font.GothamBold
    WelcomeTitle.Parent = WelcomeFrame
    
    local WelcomeMessage = Instance.new("TextLabel")
    WelcomeMessage.Name = "WelcomeMessage"
    WelcomeMessage.Text = "Loading interface..."
    WelcomeMessage.Size = UDim2.new(1, 0, 0, 30)
    WelcomeMessage.Position = UDim2.new(0, 0, 0.5, 0)
    WelcomeMessage.BackgroundTransparency = 1
    WelcomeMessage.TextColor3 = Color3.fromRGB(200, 200, 200)
    WelcomeMessage.TextSize = 16
    WelcomeMessage.Font = Enum.Font.Gotham
    WelcomeMessage.Parent = WelcomeFrame
    
    -- Animate welcome message
    WelcomeFrame.BackgroundTransparency = 1
    WelcomeTitle.TextTransparency = 1
    WelcomeMessage.TextTransparency = 1
    
    createTween(WelcomeFrame, {0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.Out}, {
        BackgroundTransparency = 0
    }):Play()
    
    wait(0.2)
    
    createTween(WelcomeTitle, {0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.Out}, {
        TextTransparency = 0
    }):Play()
    
    wait(0.2)
    
    createTween(WelcomeMessage, {0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.Out}, {
        TextTransparency = 0
    }):Play()
    
    wait(1.5)
    
    createTween(WelcomeFrame, {0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.Out}, {
        BackgroundTransparency = 1
    }):Play()
    
    createTween(WelcomeTitle, {0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.Out}, {
        TextTransparency = 1
    }):Play()
    
    createTween(WelcomeMessage, {0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.Out}, {
        TextTransparency = 1
    }):Play()
    
    wait(0.5)
    WelcomeGui:Destroy()
    
    -- Main GUI Creation
    local SolaraGUI = Instance.new("ScreenGui")
    SolaraGUI.Name = "SolaraGUI"
    SolaraGUI.Parent = CoreGui
    
    local MainFrame = Instance.new("Frame")
    MainFrame.Name = "MainFrame"
    MainFrame.Size = UDim2.new(0, 600, 0, 400)
    MainFrame.Position = UDim2.new(0.5, -300, 0.5, -200)
    MainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 35)
    MainFrame.BorderSizePixel = 0
    MainFrame.Parent = SolaraGUI
    MainFrame.BackgroundTransparency = 1
    
    local UICorner = Instance.new("UICorner")
    UICorner.CornerRadius = UDim.new(0, 8)
    UICorner.Parent = MainFrame
    
    -- Animate main GUI appearance
    createTween(MainFrame, {0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.Out}, {
        BackgroundTransparency = 0,
        Position = UDim2.new(0.5, -300, 0.5, -200)
    }):Play()
    
    -- Title Bar with gradient
    local TitleBar = Instance.new("Frame")
    TitleBar.Name = "TitleBar"
    TitleBar.Size = UDim2.new(1, 0, 0, 30)
    TitleBar.BackgroundColor3 = Color3.fromRGB(40, 40, 45)
    TitleBar.BorderSizePixel = 0
    TitleBar.Parent = MainFrame
    
    local TitleGradient = Instance.new("UIGradient")
    TitleGradient.Color = ColorSequence.new({
        ColorSequenceKeypoint.new(0, Color3.fromRGB(60, 60, 65)),
        ColorSequenceKeypoint.new(1, Color3.fromRGB(40, 40, 45))
    })
    TitleGradient.Parent = TitleBar
    
    local TitleText = Instance.new("TextLabel")
    TitleText.Name = "Title"
    TitleText.Text = windowName
    TitleText.Size = UDim2.new(1, -60, 1, 0)
    TitleText.Position = UDim2.new(0, 10, 0, 0)
    TitleText.BackgroundTransparency = 1
    TitleText.TextColor3 = Color3.fromRGB(255, 255, 255)
    TitleText.TextSize = 16
    TitleText.Font = Enum.Font.GothamBold
    TitleText.TextXAlignment = Enum.TextXAlignment.Left
    TitleText.Parent = TitleBar
    
    -- Close and Minimize Buttons with hover effects
    local CloseButton = Instance.new("TextButton")
    CloseButton.Name = "CloseButton"
    CloseButton.Size = UDim2.new(0, 30, 0, 30)
    CloseButton.Position = UDim2.new(1, -30, 0, 0)
    CloseButton.BackgroundTransparency = 1
    CloseButton.Text = "×"
    CloseButton.TextColor3 = Color3.fromRGB(255, 255, 255)
    CloseButton.TextSize = 20
    CloseButton.Font = Enum.Font.GothamBold
    CloseButton.Parent = TitleBar
    
    CloseButton.MouseEnter:Connect(function()
        createTween(CloseButton, {0.2, Enum.EasingStyle.Quad}, {
            TextColor3 = Color3.fromRGB(255, 100, 100)
        }):Play()
    end)
    
    CloseButton.MouseLeave:Connect(function()
        createTween(CloseButton, {0.2, Enum.EasingStyle.Quad}, {
            TextColor3 = Color3.fromRGB(255, 255, 255)
        }):Play()
    end)
    
    local MinimizeButton = Instance.new("TextButton")
    MinimizeButton.Name = "MinimizeButton"
    MinimizeButton.Size = UDim2.new(0, 30, 0, 30)
    MinimizeButton.Position = UDim2.new(1, -60, 0, 0)
    MinimizeButton.BackgroundTransparency = 1
    MinimizeButton.Text = "-"
    MinimizeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
    MinimizeButton.TextSize = 20
    MinimizeButton.Font = Enum.Font.GothamBold
    MinimizeButton.Parent = TitleBar
    
    MinimizeButton.MouseEnter:Connect(function()
        createTween(MinimizeButton, {0.2, Enum.EasingStyle.Quad}, {
            TextColor3 = Color3.fromRGB(200, 200, 200)
        }):Play()
    end)
    
    MinimizeButton.MouseLeave:Connect(function()
        createTween(MinimizeButton, {0.2, Enum.EasingStyle.Quad}, {
            TextColor3 = Color3.fromRGB(255, 255, 255)
        }):Play()
    end)
    
    -- Content Frame
    local ContentFrame = Instance.new("Frame")
    ContentFrame.Name = "ContentFrame"
    ContentFrame.Size = UDim2.new(1, -20, 1, -40)
    ContentFrame.Position = UDim2.new(0, 10, 0, 35)
    ContentFrame.BackgroundTransparency = 1
    ContentFrame.Parent = MainFrame
    
    -- Make window draggable with smooth dragging
    local dragging = false
    local dragStart = nil
    local startPos = nil
    local dragTween = nil
    
    TitleBar.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            dragStart = input.Position
            startPos = MainFrame.Position
            
            if dragTween then
                dragTween:Cancel()
            end
        end
    end)
    
    UserInputService.InputChanged:Connect(function(input)
        if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
            local delta = input.Position - dragStart
            local targetPosition = UDim2.new(
                startPos.X.Scale,
                startPos.X.Offset + delta.X,
                startPos.Y.Scale,
                startPos.Y.Offset + delta.Y
            )
            
            dragTween = createTween(MainFrame, {0.1, Enum.EasingStyle.Quad}, {
                Position = targetPosition
            })
            dragTween:Play()
        end
    end)
    
    UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = false
        end
    end)
    
    -- Close and Minimize functionality with animations
    local minimized = false
    local originalSize = MainFrame.Size
    
    CloseButton.MouseButton1Click:Connect(function()
        createTween(MainFrame, {0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.Out}, {
            BackgroundTransparency = 1,
            Position = UDim2.new(0.5, -300, 1.5, -200)
        }):Play()
        wait(0.5)
        SolaraGUI:Destroy()
    end)
    
    MinimizeButton.MouseButton1Click:Connect(function()
        minimized = not minimized
        if minimized then
            createTween(MainFrame, {0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.Out}, {
                Size = UDim2.new(0, 600, 0, 30)
            }):Play()
            ContentFrame.Visible = false
        else
            createTween(MainFrame, {0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.Out}, {
                Size = originalSize
            }):Play()
            ContentFrame.Visible = true
        end
    end)
    
    local window = {}
    
    -- Enhanced Toggle with animations
    function window:CreateToggle(config)
        config = config or {}
        local name = config.Name or "Toggle"
        local callback = config.Callback or function() end
        local default = config.Default or false
        
        local ToggleFrame = Instance.new("Frame")
        ToggleFrame.Name = "ToggleFrame"
        ToggleFrame.Size = UDim2.new(1, 0, 0, 40)
        ToggleFrame.BackgroundTransparency = 1
        ToggleFrame.Parent = ContentFrame
        
        local ToggleButton = Instance.new("TextButton")
        ToggleButton.Name = "ToggleButton"
        ToggleButton.Size = UDim2.new(0, 40, 0, 24)
        ToggleButton.Position = UDim2.new(0, 10, 0.5, -12)
        ToggleButton.BackgroundColor3 = Color3.fromRGB(60, 60, 65)
        ToggleButton.BorderSizePixel = 0
        ToggleButton.Text = ""
        ToggleButton.Parent = ToggleFrame
        
        local UICorner = Instance.new("UICorner")
        UICorner.CornerRadius = UDim.new(0, 12)
        UICorner.Parent = ToggleButton
        
        local ToggleCircle = Instance.new("Frame")
        ToggleCircle.Name = "ToggleCircle"
        ToggleCircle.Size = UDim2.new(0, 20, 0, 20)
        ToggleCircle.Position = UDim2.new(0, 2, 0.5, -10)
        ToggleCircle.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
        ToggleCircle.BorderSizePixel = 0
        ToggleCircle.Parent = ToggleButton
        
        local UICorner2 = Instance.new("UICorner")
        UICorner2.CornerRadius = UDim.new(1, 0)
        UICorner2.Parent = ToggleCircle
        
        local ToggleText = Instance.new("TextLabel")
        ToggleText.Name = "ToggleText"
        ToggleText.Text = name
        ToggleText.Size = UDim2.new(1, -60, 1, 0)
        ToggleText.Position = UDim2.new(0, 60, 0, 0)
        ToggleText.BackgroundTransparency = 1
        ToggleText.TextColor3 = Color3.fromRGB(255, 255, 255)
        ToggleText.TextSize = 14
        ToggleText.Font = Enum.Font.Gotham
        ToggleText.TextXAlignment = Enum.TextXAlignment.Left
        ToggleText.Parent = ToggleFrame
        
        local enabled = default
        local function updateToggle()
            if enabled then
                createTween(ToggleButton, {0.3, Enum.EasingStyle.Quad}, {
                    BackgroundColor3 = Color3.fromRGB(147, 112, 219)
                }):Play()
                createTween(ToggleCircle, {0.3, Enum.EasingStyle.Quad}, {
                    Position = UDim2.new(0, 18, 0.5, -10)
                }):Play()
            else
                createTween(ToggleButton, {0.3, Enum.EasingStyle.Quad}, {
                    BackgroundColor3 = Color3.fromRGB(60, 60, 65)
                }):Play()
                createTween(ToggleCircle, {0.3, Enum.EasingStyle.Quad}, {
                    Position = UDim2.new(0, 2, 0.5, -10)
                }):Play()
            end
            callback(enabled)
        end
        
        ToggleButton.MouseButton1Click:Connect(function()
            enabled = not enabled
            updateToggle()
        end)
        
        -- Keyboard shortcut
        local shortcutKey = config.Shortcut
        if shortcutKey then
            UserInputService.InputBegan:Connect(function(input)
                if input.KeyCode == shortcutKey and not UserInputService:GetFocusedTextBox() then
                    enabled = not enabled
                    updateToggle()
                end
            end)
        end
        
        updateToggle()
    end
    
    -- Enhanced Slider with direct input
    function window:CreateSlider(config)
        config = config or {}
        local name = config.Name or "Slider"
        local min = config.Min or 0
        local max = config.Max or 100
        local callback = config.Callback or function() end
        local default = config.Default or min
        
        local SliderFrame = Instance.new("Frame")
        SliderFrame.Name = "SliderFrame"
        SliderFrame.Size = UDim2.new(1, 0, 0, 50)
        SliderFrame.BackgroundTransparency = 1
        SliderFrame.Parent = ContentFrame
        
        local SliderText = Instance.new("TextLabel")
        SliderText.Name = "SliderText"
        SliderText.Text = name
        SliderText.Size = UDim2.new(1, -80, 0, 20)
        SliderText.Position = UDim2.new(0, 10, 0, 0)
        SliderText.BackgroundTransparency = 1
        SliderText.TextColor3 = Color3.fromRGB(255, 255, 255)
        SliderText.TextSize = 14
        SliderText.Font = Enum.Font.Gotham
        SliderText.TextXAlignment = Enum.TextXAlignment.Left
        SliderText.Parent = SliderFrame
        
        local ValueBox = Instance.new("TextBox")
        ValueBox.Name = "ValueBox"
        ValueBox.Size = UDim2.new(0, 60, 0, 20)
        ValueBox.Position = UDim2.new(1, -70, 0, 0)
        ValueBox.BackgroundColor3 = Color3.fromRGB(50, 50, 55)
        ValueBox.BorderSizePixel = 0
        ValueBox.Text = tostring(default)
        ValueBox.TextColor3 = Color3.fromRGB(255, 255, 255)
        ValueBox.TextSize = 14
        ValueBox.Font = Enum.Font.Gotham
        ValueBox.Parent = SliderFrame
        
        local UICorner3 = Instance.new("UICorner")
        UICorner3.CornerRadius = UDim.new(0, 4)
        UICorner3.Parent = ValueBox
        
        local SliderBackground = Instance.new("Frame")
        SliderBackground.Name = "SliderBackground"
        SliderBackground.Size = UDim2.new(1, -20, 0, 6)
        SliderBackground.Position = UDim2.new(0, 10, 0, 30)
        SliderBackground.BackgroundColor3 = Color3.fromRGB(60, 60, 65)
        SliderBackground.BorderSizePixel = 0
        SliderBackground.Parent = SliderFrame
        
        local UICorner = Instance.new("UICorner")
        UICorner.CornerRadius = UDim.new(0, 3)
        UICorner.Parent = SliderBackground
        
        local SliderFill = Instance.new("Frame")
        SliderFill.Name = "SliderFill"
        SliderFill.Size = UDim2.new(0, 0, 1, 0)
        SliderFill.BackgroundColor3 = Color3.fromRGB(147, 112, 219)
        SliderFill.BorderSizePixel = 0
        SliderFill.Parent = SliderBackground
        
        local UICorner2 = Instance.new("UICorner")
        UICorner2.CornerRadius = UDim.new(0, 3)
        UICorner2.Parent = SliderFill
        
        local SliderKnob = Instance.new("Frame")
        SliderKnob.Name = "SliderKnob"
        SliderKnob.Size = UDim2.new(0, 16, 0, 16)
        SliderKnob.Position = UDim2.new(0, -8, 0.5, -8)
        SliderKnob.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
        SliderKnob.BorderSizePixel = 0
        SliderKnob.Parent = SliderFill
        
        local UICorner4 = Instance.new("UICorner")
        UICorner4.CornerRadius = UDim.new(1, 0)
        UICorner4.Parent = SliderKnob
        
        local function updateSlider(value, skipTween)
            value = math.clamp(value, min, max)
            local percent = (value - min) / (max - min)
            
            if skipTween then
                SliderFill.Size = UDim2.new(percent, 0, 1, 0)
            else
                createTween(SliderFill, {0.2, Enum.EasingStyle.Quad}, {
                    Size = UDim2.new(percent, 0, 1, 0)
                }):Play()
            end
            
            ValueBox.Text = tostring(math.round(value))
            callback(value)
        end
        
        local dragging = false
        SliderBackground.InputBegan:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 then
                dragging = true
            end
        end)
        
        UserInputService.InputEnded:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 then
                dragging = false
            end
        end)
        
        UserInputService.InputChanged:Connect(function(input)
            if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
                local percent = math.clamp((input.Position.X - SliderBackground.AbsolutePosition.X) / SliderBackground.AbsoluteSize.X, 0, 1)
                local value = min + (max - min) * percent
                updateSlider(value)
            end
        end)
        
        ValueBox.FocusLost:Connect(function(enterPressed)
            if enterPressed then
                local value = tonumber(ValueBox.Text)
                if value then
                    updateSlider(value)
                else
                    ValueBox.Text = tostring(math.round(min + (max - min) * (SliderFill.Size.X.Scale)))
                end
            end
        end)
        
        updateSlider(default, true)
    end
    
    -- Enhanced Dropdown with smooth animations
    function window:CreateDropdown(config)
        config = config or {}
        local name = config.Name or "Dropdown"
        local options = config.Options or {}
        local callback = config.Callback or function() end
        local default = config.Default or options[1]
        
        local DropdownFrame = Instance.new("Frame")
        DropdownFrame.Name = "DropdownFrame"
        DropdownFrame.Size = UDim2.new(1, 0, 0, 40)
        DropdownFrame.BackgroundTransparency = 1
        DropdownFrame.Parent = ContentFrame
        
        local DropdownButton = Instance.new("TextButton")
        DropdownButton.Name = "DropdownButton"
        DropdownButton.Size = UDim2.new(1, -20, 0, 30)
        DropdownButton.Position = UDim2.new(0, 10, 0, 5)
        DropdownButton.BackgroundColor3 = Color3.fromRGB(60, 60, 65)
        DropdownButton.BorderSizePixel = 0
        DropdownButton.Text = default
        DropdownButton.TextColor3 = Color3.fromRGB(255, 255, 255)
        DropdownButton.TextSize = 14
        DropdownButton.Font = Enum.Font.Gotham
        DropdownButton.Parent = DropdownFrame
        
        local UICorner = Instance.new("UICorner")
        UICorner.CornerRadius = UDim.new(0, 4)
        UICorner.Parent = DropdownButton
        
        local OptionsFrame = Instance.new("Frame")
        OptionsFrame.Name = "OptionsFrame"
        OptionsFrame.Size = UDim2.new(1, -20, 0, 0)
        OptionsFrame.Position = UDim2.new(0, 10, 1, 5)
        OptionsFrame.BackgroundColor3 = Color3.fromRGB(50, 50, 55)
        OptionsFrame.BorderSizePixel = 0
        OptionsFrame.ClipsDescendants = true
        OptionsFrame.Visible = false
        OptionsFrame.ZIndex = 2
        OptionsFrame.Parent = DropdownFrame
        
        local UICorner2 = Instance.new("UICorner")
        UICorner2.CornerRadius = UDim.new(0, 4)
        UICorner2.Parent = OptionsFrame
        
        local OptionsLayout = Instance.new("UIListLayout")
        OptionsLayout.Parent = OptionsFrame
        OptionsLayout.SortOrder = Enum.SortOrder.LayoutOrder
        
        local function createOption(text, index)
            local OptionButton = Instance.new("TextButton")
            OptionButton.Name = "Option"
            OptionButton.Size = UDim2.new(1, 0, 0, 30)
            OptionButton.BackgroundTransparency = 1
            OptionButton.Text = text
            OptionButton.TextColor3 = Color3.fromRGB(255, 255, 255)
            OptionButton.TextSize = 14
            OptionButton.Font = Enum.Font.Gotham
            OptionButton.ZIndex = 2
            OptionButton.Parent = OptionsFrame
            
            OptionButton.MouseEnter:Connect(function()
                createTween(OptionButton, {0.2, Enum.EasingStyle.Quad}, {
                    BackgroundTransparency = 0.9
                }):Play()
            end)
            
            OptionButton.MouseLeave:Connect(function()
                createTween(OptionButton, {0.2, Enum.EasingStyle.Quad}, {
                    BackgroundTransparency = 1
                }):Play()
            end)
            
            OptionButton.MouseButton1Click:Connect(function()
                DropdownButton.Text = text
                createTween(OptionsFrame, {0.2, Enum.EasingStyle.Quad}, {
                    Size = UDim2.new(1, -20, 0, 0)
                }):Play()
                wait(0.2)
                OptionsFrame.Visible = false
                callback(text)
            end)
        end
        
        for i, option in ipairs(options) do
            createOption(option, i)
        end
        
        local dropdownOpen = false
        DropdownButton.MouseButton1Click:Connect(function()
            dropdownOpen = not dropdownOpen
            if dropdownOpen then
                OptionsFrame.Visible = true
                createTween(OptionsFrame, {0.2, Enum.EasingStyle.Quad}, {
                    Size = UDim2.new(1, -20, 0, #options * 30)
                }):Play()
            else
                createTween(OptionsFrame, {0.2, Enum.EasingStyle.Quad}, {
                    Size = UDim2.new(1, -20, 0, 0)
                }):Play()
                wait(0.2)
                OptionsFrame.Visible = false
            end
        end)
        
        UserInputService.InputBegan:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 then
                local mousePosition = UserInputService:GetMouseLocation()
                local inDropdown = mousePosition.X >= DropdownFrame.AbsolutePosition.X and
                                 mousePosition.X <= DropdownFrame.AbsolutePosition.X + DropdownFrame.AbsoluteSize.X and
                                 mousePosition.Y >= DropdownFrame.AbsolutePosition.Y and
                                 mousePosition.Y <= DropdownFrame.AbsolutePosition.Y + DropdownFrame.AbsoluteSize.Y
                
                if not inDropdown and dropdownOpen then
                    dropdownOpen = false
                    createTween(OptionsFrame, {0.2, Enum.EasingStyle.Quad}, {
                        Size = UDim2.new(1, -20, 0, 0)
                    }):Play()
                    wait(0.2)
                    OptionsFrame.Visible = false
                end
            end
        end)
    end
    
    return window
end

return SolaraLibrary
