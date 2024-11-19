local SolaraLibrary = {}

local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local CoreGui = game:GetService("CoreGui")
local RunService = game:GetService("RunService")

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
    MainFrame.Size = UDim2.new(0, 600, 0, 400)
    MainFrame.Position = UDim2.new(0.5, -300, 0.5, -200)
    MainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 35)
    MainFrame.BorderSizePixel = 0
    MainFrame.Parent = SolaraGUI
    
    local UICorner = Instance.new("UICorner")
    UICorner.CornerRadius = UDim.new(0, 8)
    UICorner.Parent = MainFrame
    
    -- Title Bar
    local TitleBar = Instance.new("Frame")
    TitleBar.Name = "TitleBar"
    TitleBar.Size = UDim2.new(1, 0, 0, 30)
    TitleBar.BackgroundColor3 = Color3.fromRGB(40, 40, 45)
    TitleBar.BorderSizePixel = 0
    TitleBar.Parent = MainFrame
    
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
    
    -- Close and Minimize Buttons
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
    
    -- Content Frame
    local ContentFrame = Instance.new("Frame")
    ContentFrame.Name = "ContentFrame"
    ContentFrame.Size = UDim2.new(1, -20, 1, -40)
    ContentFrame.Position = UDim2.new(0, 10, 0, 35)
    ContentFrame.BackgroundTransparency = 1
    ContentFrame.Parent = MainFrame
    
    -- Make window draggable
    local dragging = false
    local dragStart = nil
    local startPos = nil
    
    TitleBar.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            dragStart = input.Position
            startPos = MainFrame.Position
        end
    end)
    
    UserInputService.InputChanged:Connect(function(input)
        if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
            local delta = input.Position - dragStart
            MainFrame.Position = UDim2.new(
                startPos.X.Scale,
                startPos.X.Offset + delta.X,
                startPos.Y.Scale,
                startPos.Y.Offset + delta.Y
            )
        end
    end)
    
    UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = false
        end
    end)
    
    -- Close and Minimize functionality
    local minimized = false
    local originalSize = MainFrame.Size
    
    CloseButton.MouseButton1Click:Connect(function()
        SolaraGUI:Destroy()
    end)
    
    MinimizeButton.MouseButton1Click:Connect(function()
        minimized = not minimized
        if minimized then
            MainFrame.Size = UDim2.new(0, 600, 0, 30)
            ContentFrame.Visible = false
        else
            MainFrame.Size = originalSize
            ContentFrame.Visible = true
        end
    end)
    
    local window = {}
    
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
        ToggleButton.Size = UDim2.new(0, 24, 0, 24)
        ToggleButton.Position = UDim2.new(0, 10, 0.5, -12)
        ToggleButton.BackgroundColor3 = Color3.fromRGB(60, 60, 65)
        ToggleButton.BorderSizePixel = 0
        ToggleButton.Text = ""
        ToggleButton.Parent = ToggleFrame
        
        local UICorner = Instance.new("UICorner")
        UICorner.CornerRadius = UDim.new(0, 4)
        UICorner.Parent = ToggleButton
        
        local ToggleText = Instance.new("TextLabel")
        ToggleText.Name = "ToggleText"
        ToggleText.Text = name
        ToggleText.Size = UDim2.new(1, -50, 1, 0)
        ToggleText.Position = UDim2.new(0, 40, 0, 0)
        ToggleText.BackgroundTransparency = 1
        ToggleText.TextColor3 = Color3.fromRGB(255, 255, 255)
        ToggleText.TextSize = 14
        ToggleText.Font = Enum.Font.Gotham
        ToggleText.TextXAlignment = Enum.TextXAlignment.Left
        ToggleText.Parent = ToggleFrame
        
        local enabled = default
        local function updateToggle()
            if enabled then
                ToggleButton.BackgroundColor3 = Color3.fromRGB(147, 112, 219) -- Purple when enabled
            else
                ToggleButton.BackgroundColor3 = Color3.fromRGB(60, 60, 65)
            end
            callback(enabled)
        end
        
        ToggleButton.MouseButton1Click:Connect(function()
            enabled = not enabled
            updateToggle()
        end)
        
        updateToggle()
    end
    
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
        SliderText.Size = UDim2.new(1, -20, 0, 20)
        SliderText.Position = UDim2.new(0, 10, 0, 0)
        SliderText.BackgroundTransparency = 1
        SliderText.TextColor3 = Color3.fromRGB(255, 255, 255)
        SliderText.TextSize = 14
        SliderText.Font = Enum.Font.Gotham
        SliderText.TextXAlignment = Enum.TextXAlignment.Left
        SliderText.Parent = SliderFrame
        
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
        SliderFill.BackgroundColor3 = Color3.fromRGB(147, 112, 219) -- Purple
        SliderFill.BorderSizePixel = 0
        SliderFill.Parent = SliderBackground
        
        local UICorner2 = Instance.new("UICorner")
        UICorner2.CornerRadius = UDim.new(0, 3)
        UICorner2.Parent = SliderFill
        
        local ValueText = Instance.new("TextLabel")
        ValueText.Name = "ValueText"
        ValueText.Text = tostring(default)
        ValueText.Size = UDim2.new(0, 50, 0, 20)
        ValueText.Position = UDim2.new(1, -60, 0, 0)
        ValueText.BackgroundTransparency = 1
        ValueText.TextColor3 = Color3.fromRGB(255, 255, 255)
        ValueText.TextSize = 14
        ValueText.Font = Enum.Font.Gotham
        ValueText.Parent = SliderFrame
        
        local function updateSlider(value)
            value = math.clamp(value, min, max)
            local percent = (value - min) / (max - min)
            SliderFill.Size = UDim2.new(percent, 0, 1, 0)
            ValueText.Text = tostring(math.round(value))
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
        
        updateSlider(default)
    end
    
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
        OptionsFrame.Size = UDim2.new(1, -20, 0, #options * 30)
        OptionsFrame.Position = UDim2.new(0, 10, 1, 5)
        OptionsFrame.BackgroundColor3 = Color3.fromRGB(50, 50, 55)
        OptionsFrame.BorderSizePixel = 0
        OptionsFrame.Visible = false
        OptionsFrame.ZIndex = 2
        OptionsFrame.Parent = DropdownFrame
        
        local UICorner2 = Instance.new("UICorner")
        UICorner2.CornerRadius = UDim.new(0, 4)
        UICorner2.Parent = OptionsFrame
        
        local function createOption(text, index)
            local OptionButton = Instance.new("TextButton")
            OptionButton.Name = "Option"
            OptionButton.Size = UDim2.new(1, 0, 0, 30)
            OptionButton.Position = UDim2.new(0, 0, 0, (index - 1) * 30)
            OptionButton.BackgroundTransparency = 1
            OptionButton.Text = text
            OptionButton.TextColor3 = Color3.fromRGB(255, 255, 255)
            OptionButton.TextSize = 14
            OptionButton.Font = Enum.Font.Gotham
            OptionButton.ZIndex = 2
            OptionButton.Parent = OptionsFrame
            
            OptionButton.MouseButton1Click:Connect(function()
                DropdownButton.Text = text
                OptionsFrame.Visible = false
                callback(text)
            end)
        end
        
        for i, option in ipairs(options) do
            createOption(option, i)
        end
        
        DropdownButton.MouseButton1Click:Connect(function()
            OptionsFrame.Visible = not OptionsFrame.Visible
        end)
        
        UserInputService.InputBegan:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 then
                local mousePosition = UserInputService:GetMouseLocation()
                local inFrame = mousePosition.X >= DropdownFrame.AbsolutePosition.X and
                               mousePosition.X <= DropdownFrame.AbsolutePosition.X + DropdownFrame.AbsoluteSize.X and
                               mousePosition.Y >= DropdownFrame.AbsolutePosition.Y and
                               mousePosition.Y <= DropdownFrame.AbsolutePosition.Y + DropdownFrame.AbsoluteSize.Y
                
                if not inFrame and OptionsFrame.Visible then
                    OptionsFrame.Visible = false
                end
            end
        end)
    end
    
    return window
end

return SolaraLibrary
