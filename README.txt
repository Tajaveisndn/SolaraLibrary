local Solar4 = {}
Solar4.__index = Solar4

-- Services
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

-- Constants
local THEME = {
    Primary = Color3.fromRGB(147, 112, 219),    -- Purple
    Secondary = Color3.fromRGB(138, 43, 226),   -- Deep Purple
    Background = Color3.fromRGB(30, 30, 35),    -- Dark Gray
    Text = Color3.fromRGB(255, 255, 255),       -- White
    Border = Color3.fromRGB(123, 104, 238),     -- Medium Purple
    Hover = Color3.fromRGB(153, 50, 204),       -- Dark Orchid
}

-- Utility Functions
local function CreateTween(instance, properties, duration, style, direction)
    local tweenInfo = TweenInfo.new(
        duration or 0.3,
        style or Enum.EasingStyle.Quad,
        direction or Enum.EasingDirection.Out
    )
    local tween = TweenService:Create(instance, tweenInfo, properties)
    return tween
end

local function DragifyFrame(frame, dragPart)
    local dragging = false
    local dragInput
    local dragStart
    local startPos

    local function UpdateDrag(input)
        local delta = input.Position - dragStart
        frame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X,
            startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end

    dragPart.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            dragStart = input.Position
            startPos = frame.Position
            
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragging = false
                end
            end)
        end
    end)

    dragPart.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement then
            dragInput = input
        end
    end)

    UserInputService.InputChanged:Connect(function(input)
        if input == dragInput and dragging then
            UpdateDrag(input)
        end
    end)
end

-- Core UI Creation Function
function Solar4.new()
    local library = setmetatable({}, Solar4)
    library.Toggled = true
    library.Elements = {}
    
    -- Main GUI Creation
    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.Name = "Solar4Library"
    ScreenGui.ResetOnSpawn = false
    ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    
    -- Main Frame
    local MainFrame = Instance.new("Frame")
    MainFrame.Name = "MainFrame"
    MainFrame.Size = UDim2.new(0, 300, 0, 350)
    MainFrame.Position = UDim2.new(0.5, -150, 0.5, -175)
    MainFrame.BackgroundColor3 = THEME.Background
    MainFrame.BorderSizePixel = 0
    MainFrame.ClipsDescendants = true
    
    -- Top Bar
    local TopBar = Instance.new("Frame")
    TopBar.Name = "TopBar"
    TopBar.Size = UDim2.new(1, 0, 0, 30)
    TopBar.BackgroundColor3 = THEME.Primary
    TopBar.BorderSizePixel = 0
    TopBar.Parent = MainFrame
    
    -- Title
    local Title = Instance.new("TextLabel")
    Title.Name = "Title"
    Title.Size = UDim2.new(1, -60, 1, 0)
    Title.Position = UDim2.new(0, 10, 0, 0)
    Title.BackgroundTransparency = 1
    Title.Text = "Solar4 Library"
    Title.TextColor3 = THEME.Text
    Title.TextSize = 16
    Title.Font = Enum.Font.GothamBold
    Title.TextXAlignment = Enum.TextXAlignment.Left
    Title.Parent = TopBar
    
    -- Close Button
    local CloseButton = Instance.new("TextButton")
    CloseButton.Name = "CloseButton"
    CloseButton.Size = UDim2.new(0, 30, 0, 30)
    CloseButton.Position = UDim2.new(1, -30, 0, 0)
    CloseButton.BackgroundTransparency = 1
    CloseButton.Text = "×"
    CloseButton.TextColor3 = THEME.Text
    CloseButton.TextSize = 20
    CloseButton.Font = Enum.Font.GothamBold
    CloseButton.Parent = TopBar
    
    -- Minimize Button
    local MinimizeButton = Instance.new("TextButton")
    MinimizeButton.Name = "MinimizeButton"
    MinimizeButton.Size = UDim2.new(0, 30, 0, 30)
    MinimizeButton.Position = UDim2.new(1, -60, 0, 0)
    MinimizeButton.BackgroundTransparency = 1
    MinimizeButton.Text = "-"
    MinimizeButton.TextColor3 = THEME.Text
    MinimizeButton.TextSize = 20
    MinimizeButton.Font = Enum.Font.GothamBold
    MinimizeButton.Parent = TopBar
    
    -- Content Container
    local Container = Instance.new("ScrollingFrame")
    Container.Name = "Container"
    Container.Size = UDim2.new(1, -20, 1, -40)
    Container.Position = UDim2.new(0, 10, 0, 35)
    Container.BackgroundTransparency = 1
    Container.BorderSizePixel = 0
    Container.ScrollBarThickness = 4
    Container.ScrollBarImageColor3 = THEME.Primary
    Container.Parent = MainFrame
    
    -- Auto-size list layout
    local UIListLayout = Instance.new("UIListLayout")
    UIListLayout.Padding = UDim.new(0, 5)
    UIListLayout.Parent = Container
    
    -- Make the frame draggable
    DragifyFrame(MainFrame, TopBar)
    
    -- Setup minimize functionality
    MinimizeButton.MouseButton1Click:Connect(function()
        library.Toggled = not library.Toggled
        local targetSize = library.Toggled and UDim2.new(1, -20, 1, -40) or UDim2.new(1, -20, 0, 0)
        CreateTween(Container, {Size = targetSize}, 0.3):Play()
    end)
    
    -- Setup close functionality
    CloseButton.MouseButton1Click:Connect(function()
        ScreenGui:Destroy()
    end)
    
    -- Store references
    library.ScreenGui = ScreenGui
    library.MainFrame = MainFrame
    library.Container = Container
    
    -- Parent the ScreenGui
    if RunService:IsStudio() then
        ScreenGui.Parent = game.Players.LocalPlayer.PlayerGui
    else
        ScreenGui.Parent = game.CoreGui
    end
    
    return library
end

return Solar4
-- Toggle and Switch Components
function Solar4:CreateToggle(text, callback)
    local toggle = {
        Value = false,
        Type = "Toggle"
    }
    
    -- Create main container
    local ToggleContainer = Instance.new("Frame")
    ToggleContainer.Name = "ToggleContainer"
    ToggleContainer.Size = UDim2.new(1, 0, 0, 35)
    ToggleContainer.BackgroundColor3 = THEME.Background
    ToggleContainer.BorderSizePixel = 0
    ToggleContainer.Parent = self.Container
    
    -- Add hover effect
    local UIStroke = Instance.new("UIStroke")
    UIStroke.Color = THEME.Border
    UIStroke.Transparency = 0.9
    UIStroke.Parent = ToggleContainer
    
    -- Toggle label
    local Label = Instance.new("TextLabel")
    Label.Name = "Label"
    Label.Size = UDim2.new(1, -60, 1, 0)
    Label.Position = UDim2.new(0, 10, 0, 0)
    Label.BackgroundTransparency = 1
    Label.Text = text
    Label.TextColor3 = THEME.Text
    Label.TextSize = 14
    Label.Font = Enum.Font.Gotham
    Label.TextXAlignment = Enum.TextXAlignment.Left
    Label.Parent = ToggleContainer
    
    -- Toggle button background
    local ToggleBackground = Instance.new("Frame")
    ToggleBackground.Name = "ToggleBackground"
    ToggleBackground.Size = UDim2.new(0, 40, 0, 20)
    ToggleBackground.Position = UDim2.new(1, -50, 0.5, -10)
    ToggleBackground.BackgroundColor3 = THEME.Secondary
    ToggleBackground.BackgroundTransparency = 0.8
    ToggleBackground.BorderSizePixel = 0
    ToggleBackground.Parent = ToggleContainer
    
    -- Make background rounded
    local UICorner = Instance.new("UICorner")
    UICorner.CornerRadius = UDim.new(1, 0)
    UICorner.Parent = ToggleBackground
    
    -- Toggle indicator
    local Indicator = Instance.new("Frame")
    Indicator.Name = "Indicator"
    Indicator.Size = UDim2.new(0, 16, 0, 16)
    Indicator.Position = UDim2.new(0, 2, 0.5, -8)
    Indicator.BackgroundColor3 = THEME.Text
    Indicator.BorderSizePixel = 0
    Indicator.Parent = ToggleBackground
    
    -- Make indicator rounded
    local UICorner2 = Instance.new("UICorner")
    UICorner2.CornerRadius = UDim.new(1, 0)
    UICorner2.Parent = Indicator
    
    -- Add ripple effect
    local RippleContainer = Instance.new("Frame")
    RippleContainer.Name = "RippleContainer"
    RippleContainer.Size = UDim2.new(1, 0, 1, 0)
    RippleContainer.BackgroundTransparency = 1
    RippleContainer.ClipsDescendants = true
    RippleContainer.Parent = ToggleContainer
    
    local function CreateRipple(x, y)
        local Ripple = Instance.new("Frame")
        Ripple.Position = UDim2.new(0, x, 0, y)
        Ripple.Size = UDim2.new(0, 0, 0, 0)
        Ripple.AnchorPoint = Vector2.new(0.5, 0.5)
        Ripple.BackgroundColor3 = THEME.Primary
        Ripple.BackgroundTransparency = 0.6
        Ripple.BorderSizePixel = 0
        Ripple.Parent = RippleContainer
        
        local RippleCorner = Instance.new("UICorner")
        RippleCorner.CornerRadius = UDim.new(1, 0)
        RippleCorner.Parent = Ripple
        
        local RippleAnimation = TweenService:Create(Ripple, TweenInfo.new(0.5), {
            Size = UDim2.new(0, 100, 0, 100),
            BackgroundTransparency = 1
        })
        
        RippleAnimation:Play()
        RippleAnimation.Completed:Connect(function()
            Ripple:Destroy()
        end)
    end
    
    -- Toggle functionality
    local function UpdateToggle()
        local pos = toggle.Value and UDim2.new(1, -18, 0.5, -8) or UDim2.new(0, 2, 0.5, -8)
        local color = toggle.Value and THEME.Primary or THEME.Secondary
        
        CreateTween(Indicator, {Position = pos}, 0.3):Play()
        CreateTween(ToggleBackground, {BackgroundColor3 = color, BackgroundTransparency = toggle.Value and 0 or 0.8}, 0.3):Play()
        
        if callback then
            callback(toggle.Value)
        end
    end
    
    -- Click handling
    ToggleContainer.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            toggle.Value = not toggle.Value
            UpdateToggle()
            
            -- Create ripple effect at mouse position
            local relativeX = input.Position.X - ToggleContainer.AbsolutePosition.X
            local relativeY = input.Position.Y - ToggleContainer.AbsolutePosition.Y
            CreateRipple(relativeX, relativeY)
        end
    end)
    
    -- Hover effects
    ToggleContainer.MouseEnter:Connect(function()
        CreateTween(UIStroke, {Transparency = 0.7}, 0.2):Play()
    end)
    
    ToggleContainer.MouseLeave:Connect(function()
        CreateTween(UIStroke, {Transparency = 0.9}, 0.2):Play()
    end)
    
    -- Add to library elements
    table.insert(self.Elements, toggle)
    
    -- Return toggle object
    return toggle
end

-- Create Switch (Alternative style toggle)
function Solar4:CreateSwitch(text, callback)
    local switch = {
        Value = false,
        Type = "Switch"
    }
    
    -- Create main container
    local SwitchContainer = Instance.new("Frame")
    SwitchContainer.Name = "SwitchContainer"
    SwitchContainer.Size = UDim2.new(1, 0, 0, 35)
    SwitchContainer.BackgroundColor3 = THEME.Background
    SwitchContainer.BorderSizePixel = 0
    SwitchContainer.Parent = self.Container
    
    -- Add hover glow
    local UIGlow = Instance.new("ImageLabel")
    UIGlow.Name = "Glow"
    UIGlow.BackgroundTransparency = 1
    UIGlow.Position = UDim2.new(0, -15, 0, -15)
    UIGlow.Size = UDim2.new(1, 30, 1, 30)
    UIGlow.Image = "rbxassetid://7603818383"
    UIGlow.ImageColor3 = THEME.Primary
    UIGlow.ImageTransparency = 1
    UIGlow.Parent = SwitchContainer
    
    -- Switch label
    local Label = Instance.new("TextLabel")
    Label.Name = "Label"
    Label.Size = UDim2.new(1, -60, 1, 0)
    Label.Position = UDim2.new(0, 10, 0, 0)
    Label.BackgroundTransparency = 1
    Label.Text = text
    Label.TextColor3 = THEME.Text
    Label.TextSize = 14
    Label.Font = Enum.Font.Gotham
    Label.TextXAlignment = Enum.TextXAlignment.Left
    Label.Parent = SwitchContainer
    
    -- Switch button
    local SwitchButton = Instance.new("Frame")
    SwitchButton.Name = "SwitchButton"
    SwitchButton.Size = UDim2.new(0, 45, 0, 25)
    SwitchButton.Position = UDim2.new(1, -55, 0.5, -12.5)
    SwitchButton.BackgroundColor3 = THEME.Secondary
    SwitchButton.BackgroundTransparency = 0.8
    SwitchButton.BorderSizePixel = 0
    SwitchButton.Parent = SwitchContainer
    
    local UICorner = Instance.new("UICorner")
    UICorner.CornerRadius = UDim.new(0, 8)
    UICorner.Parent = SwitchButton
    
    -- Switch knob
    local Knob = Instance.new("Frame")
    Knob.Name = "Knob"
    Knob.Size = UDim2.new(0, 21, 0, 21)
    Knob.Position = UDim2.new(0, 2, 0.5, -10.5)
    Knob.BackgroundColor3 = THEME.Text
    Knob.BorderSizePixel = 0
    Knob.Parent = SwitchButton
    
    local KnobCorner = Instance.new("UICorner")
    KnobCorner.CornerRadius = UDim.new(0, 6)
    KnobCorner.Parent = Knob
    
    -- Switch functionality
    local function UpdateSwitch()
        local pos = switch.Value and UDim2.new(1, -23, 0.5, -10.5) or UDim2.new(0, 2, 0.5, -10.5)
        local color = switch.Value and THEME.Primary or THEME.Secondary
        
        CreateTween(Knob, {
            Position = pos,
            BackgroundColor3 = switch.Value and THEME.Text or THEME.Text
        }, 0.3):Play()
        
        CreateTween(SwitchButton, {
            BackgroundColor3 = color,
            BackgroundTransparency = switch.Value and 0 or 0.8
        }, 0.3):Play()
        
        if callback then
            callback(switch.Value)
        end
    end
    
    -- Click handling with animations
    SwitchContainer.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            switch.Value = not switch.Value
            UpdateSwitch()
            
            -- Knob press animation
            CreateTween(Knob, {
                Size = UDim2.new(0, 19, 0, 19),
                Position = switch.Value 
                    and UDim2.new(1, -22, 0.5, -9.5) 
                    or UDim2.new(0, 3, 0.5, -9.5)
            }, 0.1):Play()
            
            wait(0.1)
            
            CreateTween(Knob, {
                Size = UDim2.new(0, 21, 0, 21),
                Position = switch.Value 
                    and UDim2.new(1, -23, 0.5, -10.5) 
                    or UDim2.new(0, 2, 0.5, -10.5)
            }, 0.1):Play()
        end
    end)
    
    -- Hover effects
    SwitchContainer.MouseEnter:Connect(function()
        CreateTween(UIGlow, {ImageTransparency = 0.9}, 0.2):Play()
    end)
    
    SwitchContainer.MouseLeave:Connect(function()
        CreateTween(UIGlow, {ImageTransparency = 1}, 0.2):Play()
    end)
    
    -- Add to library elements
    table.insert(self.Elements, switch)
    
    return switch
end
-- Slider Component
function Solar4:CreateSlider(text, min, max, default, callback)
    local slider = {
        Value = default or min,
        Type = "Slider",
        Min = min,
        Max = max
    }
    
    -- Create main container
    local SliderContainer = Instance.new("Frame")
    SliderContainer.Name = "SliderContainer"
    SliderContainer.Size = UDim2.new(1, 0, 0, 50)
    SliderContainer.BackgroundColor3 = THEME.Background
    SliderContainer.BorderSizePixel = 0
    SliderContainer.Parent = self.Container
    
    -- Slider label
    local Label = Instance.new("TextLabel")
    Label.Name = "Label"
    Label.Size = UDim2.new(1, -10, 0, 20)
    Label.Position = UDim2.new(0, 10, 0, 0)
    Label.BackgroundTransparency = 1
    Label.Text = text
    Label.TextColor3 = THEME.Text
    Label.TextSize = 14
    Label.Font = Enum.Font.Gotham
    Label.TextXAlignment = Enum.TextXAlignment.Left
    Label.Parent = SliderContainer
    
    -- Value display
    local ValueLabel = Instance.new("TextLabel")
    ValueLabel.Name = "ValueLabel"
    ValueLabel.Size = UDim2.new(0, 50, 0, 20)
    ValueLabel.Position = UDim2.new(1, -60, 0, 0)
    ValueLabel.BackgroundTransparency = 1
    ValueLabel.Text = tostring(slider.Value)
    ValueLabel.TextColor3 = THEME.Text
    ValueLabel.TextSize = 14
    ValueLabel.Font = Enum.Font.GothamBold
    ValueLabel.TextXAlignment = Enum.TextXAlignment.Right
    ValueLabel.Parent = SliderContainer
    
    -- Slider background
    local SliderBackground = Instance.new("Frame")
    SliderBackground.Name = "SliderBackground"
    SliderBackground.Size = UDim2.new(1, -20, 0, 6)
    SliderBackground.Position = UDim2.new(0, 10, 0, 35)
    SliderBackground.BackgroundColor3 = THEME.Secondary
    SliderBackground.BackgroundTransparency = 0.8
    SliderBackground.BorderSizePixel = 0
    SliderBackground.Parent = SliderContainer
    
    local UICorner = Instance.new("UICorner")
    UICorner.CornerRadius = UDim.new(1, 0)
    UICorner.Parent = SliderBackground
    
    -- Slider fill
    local SliderFill = Instance.new("Frame")
    SliderFill.Name = "SliderFill"
    SliderFill.Size = UDim2.new(0, 0, 1, 0)
    SliderFill.BackgroundColor3 = THEME.Primary
    SliderFill.BorderSizePixel = 0
    SliderFill.Parent = SliderBackground
    
    local UICorner2 = Instance.new("UICorner")
    UICorner2.CornerRadius = UDim.new(1, 0)
    UICorner2.Parent = SliderFill
    
    -- Slider knob
    local Knob = Instance.new("Frame")
    Knob.Name = "Knob"
    Knob.Size = UDim2.new(0, 16, 0, 16)
    Knob.Position = UDim2.new(0, -8, 0.5, -8)
    Knob.BackgroundColor3 = THEME.Primary
    Knob.BorderSizePixel = 0
    Knob.ZIndex = 2
    Knob.Parent = SliderFill
    
    local KnobCorner = Instance.new("UICorner")
    KnobCorner.CornerRadius = UDim.new(1, 0)
    KnobCorner.Parent = Knob
    
    -- Knob glow effect
    local KnobGlow = Instance.new("ImageLabel")
    KnobGlow.Name = "KnobGlow"
    KnobGlow.BackgroundTransparency = 1
    KnobGlow.Position = UDim2.new(0.5, -15, 0.5, -15)
    KnobGlow.Size = UDim2.new(0, 30, 0, 30)
    KnobGlow.Image = "rbxassetid://7603818383"
    KnobGlow.ImageColor3 = THEME.Primary
    KnobGlow.ImageTransparency = 1
    KnobGlow.Parent = Knob
    
    -- Update slider function
    local function UpdateSlider(value)
        value = math.clamp(value, min, max)
        slider.Value = value
        
        local percent = (value - min) / (max - min)
        SliderFill.Size = UDim2.new(percent, 0, 1, 0)
        ValueLabel.Text = tostring(math.floor(value))
        
        if callback then
            callback(value)
        end
    end
    
    -- Initial setup
    UpdateSlider(slider.Value)
    
    -- Slider interaction
    local isDragging = false
    
    SliderBackground.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            isDragging = true
            
            -- Show knob glow
            CreateTween(KnobGlow, {ImageTransparency = 0.7}, 0.2):Play()
            
            -- Calculate value from mouse position
            local percent = math.clamp(
                (input.Position.X - SliderBackground.AbsolutePosition.X) / SliderBackground.AbsoluteSize.X,
                0, 1
            )
            local value = min + (max - min) * percent
            UpdateSlider(value)
        end
    end)
    
    UserInputService.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement and isDragging then
            -- Calculate value from mouse position
            local percent = math.clamp(
                (input.Position.X - SliderBackground.AbsolutePosition.X) / SliderBackground.AbsoluteSize.X,
                0, 1
            )
            local value = min + (max - min) * percent
            UpdateSlider(value)
        end
    end)
    
    UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            isDragging = false
            
            -- Hide knob glow
            CreateTween(KnobGlow, {ImageTransparency = 1}, 0.2):Play()
        end
    end)
    
    -- Add to library elements
    table.insert(self.Elements, slider)
    
    return slider
end

-- Button Component
function Solar4:CreateButton(text, callback)
    local button = {
        Type = "Button"
    }
    
    -- Create main container
    local ButtonContainer = Instance.new("Frame")
    ButtonContainer.Name = "ButtonContainer"
    ButtonContainer.Size = UDim2.new(1, 0, 0, 35)
    ButtonContainer.BackgroundColor3 = THEME.Primary
    ButtonContainer.BackgroundTransparency = 0.8
    ButtonContainer.BorderSizePixel = 0
    ButtonContainer.Parent = self.Container
    
    -- Add corner radius
    local UICorner = Instance.new("UICorner")
    UICorner.CornerRadius = UDim.new(0, 6)
    UICorner.Parent = ButtonContainer
    
    -- Button label
    local Label = Instance.new("TextLabel")
    Label.Name = "Label"
    Label.Size = UDim2.new(1, 0, 1, 0)
    Label.BackgroundTransparency = 1
    Label.Text = text
    Label.TextColor3 = THEME.Text
    Label.TextSize = 14
    Label.Font = Enum.Font.GothamBold
    Label.Parent = ButtonContainer
    
    -- Ripple container
    local RippleContainer = Instance.new("Frame")
    RippleContainer.Name = "RippleContainer"
    RippleContainer.Size = UDim2.new(1, 0, 1, 0)
    RippleContainer.BackgroundTransparency = 1
    RippleContainer.ClipsDescendants = true
    RippleContainer.Parent = ButtonContainer
    
    -- Create ripple effect
    local function CreateRipple(x, y)
        local Ripple = Instance.new("Frame")
        Ripple.Position = UDim2.new(0, x, 0, y)
        Ripple.Size = UDim2.new(0, 0, 0, 0)
        Ripple.AnchorPoint = Vector2.new(0.5, 0.5)
        Ripple.BackgroundColor3 = THEME.Text
        Ripple.BackgroundTransparency = 0.6
        Ripple.BorderSizePixel = 0
        Ripple.ZIndex = 2
        Ripple.Parent = RippleContainer
        
        local RippleCorner = Instance.new("UICorner")
        RippleCorner.CornerRadius = UDim.new(1, 0)
        RippleCorner.Parent = Ripple
        
        local size = math.max(ButtonContainer.AbsoluteSize.X, ButtonContainer.AbsoluteSize.Y) * 1.5
        
        local RippleAnimation = TweenService:Create(Ripple, TweenInfo.new(0.5), {
            Size = UDim2.new(0, size, 0, size),
            BackgroundTransparency = 1
        })
        
        RippleAnimation:Play()
        RippleAnimation.Completed:Connect(function()
            Ripple:Destroy()
        end)
    end
    
    -- Button functionality
    ButtonContainer.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            -- Create ripple effect
            local relativeX = input.Position.X - ButtonContainer.AbsolutePosition.X
            local relativeY = input.Position.Y - ButtonContainer.AbsolutePosition.Y
            CreateRipple(relativeX, relativeY)
            
            -- Button press animation
            CreateTween(ButtonContainer, {
                BackgroundTransparency = 0.6,
                Size = UDim2.new(1, 0, 0, 33)
            }, 0.1):Play()
            
            -- Execute callback
            if callback then
                callback()
            end
            
            wait(0.1)
            
            CreateTween(ButtonContainer, {
                BackgroundTransparency = 0.8,
                Size = UDim2.new(1, 0, 0, 35)
            }, 0.1):Play()
        end
    end)
    
    -- Hover effects
    ButtonContainer.MouseEnter:Connect(function()
        CreateTween(ButtonContainer, {BackgroundTransparency = 0.7}, 0.2):Play()
    end)
    
    ButtonContainer.MouseLeave:Connect(function()
        CreateTween(ButtonContainer, {BackgroundTransparency = 0.8}, 0.2):Play()
    end)
    
    -- Add to library elements
    table.insert(self.Elements, button)
    
    return button
end
-- Dropdown Component
function Solar4:CreateDropdown(text, options, default, callback)
    local dropdown = {
        Value = default or options[1],
        Type = "Dropdown",
        Options = options,
        Open = false
    }
    
    -- Create main container
    local DropdownContainer = Instance.new("Frame")
    DropdownContainer.Name = "DropdownContainer"
    DropdownContainer.Size = UDim2.new(1, 0, 0, 35)
    DropdownContainer.BackgroundColor3 = THEME.Background
    DropdownContainer.BorderSizePixel = 0
    DropdownContainer.ClipsDescendants = true
    DropdownContainer.Parent = self.Container
    
    -- Dropdown header
    local Header = Instance.new("Frame")
    Header.Name = "Header"
    Header.Size = UDim2.new(1, 0, 0, 35)
    Header.BackgroundColor3 = THEME.Secondary
    Header.BackgroundTransparency = 0.8
    Header.BorderSizePixel = 0
    Header.Parent = DropdownContainer
    
    local UICorner = Instance.new("UICorner")
    UICorner.CornerRadius = UDim.new(0, 6)
    UICorner.Parent = Header
    
    -- Dropdown label
    local Label = Instance.new("TextLabel")
    Label.Name = "Label"
    Label.Size = UDim2.new(1, -30, 1, 0)
    Label.Position = UDim2.new(0, 10, 0, 0)
    Label.BackgroundTransparency = 1
    Label.Text = text
    Label.TextColor3 = THEME.Text
    Label.TextSize = 14
    Label.Font = Enum.Font.Gotham
    Label.TextXAlignment = Enum.TextXAlignment.Left
    Label.Parent = Header
    
    -- Selected value display
    local SelectedValue = Instance.new("TextLabel")
    SelectedValue.Name = "SelectedValue"
    SelectedValue.Size = UDim2.new(0, 100, 1, 0)
    SelectedValue.Position = UDim2.new(1, -110, 0, 0)
    SelectedValue.BackgroundTransparency = 1
    SelectedValue.Text = tostring(dropdown.Value)
    SelectedValue.TextColor3 = THEME.Primary
    SelectedValue.TextSize = 14
    SelectedValue.Font = Enum.Font.GothamBold
    SelectedValue.TextXAlignment = Enum.TextXAlignment.Right
    SelectedValue.Parent = Header
    
    -- Arrow indicator
    local Arrow = Instance.new("ImageLabel")
    Arrow.Name = "Arrow"
    Arrow.Size = UDim2.new(0, 20, 0, 20)
    Arrow.Position = UDim2.new(1, -25, 0.5, -10)
    Arrow.BackgroundTransparency = 1
    Arrow.Image = "rbxassetid://7072706796"
    Arrow.ImageColor3 = THEME.Text
    Arrow.Parent = Header
    
    -- Options container
    local OptionsContainer = Instance.new("Frame")
    OptionsContainer.Name = "OptionsContainer"
    OptionsContainer.Size = UDim2.new(1, -10, 0, 0)
    OptionsContainer.Position = UDim2.new(0, 5, 0, 40)
    OptionsContainer.BackgroundColor3 = THEME.Background
    OptionsContainer.BorderSizePixel = 0
    OptionsContainer.ClipsDescendants = true
    OptionsContainer.Parent = DropdownContainer
    
    -- Options list layout
    local UIListLayout = Instance.new("UIListLayout")
    UIListLayout.Padding = UDim.new(0, 5)
    UIListLayout.Parent = OptionsContainer
    
    -- Create option buttons
    local function CreateOption(optionText)
        local Option = Instance.new("TextButton")
        Option.Name = "Option"
        Option.Size = UDim2.new(1, 0, 0, 30)
        Option.BackgroundColor3 = THEME.Secondary
        Option.BackgroundTransparency = 0.9
        Option.BorderSizePixel = 0
        Option.Text = optionText
        Option.TextColor3 = THEME.Text
        Option.TextSize = 14
        Option.Font = Enum.Font.Gotham
        Option.Parent = OptionsContainer
        
        local OptionCorner = Instance.new("UICorner")
        OptionCorner.CornerRadius = UDim.new(0, 4)
        OptionCorner.Parent = Option
        
        -- Option hover effects
        Option.MouseEnter:Connect(function()
            CreateTween(Option, {BackgroundTransparency = 0.8}, 0.2):Play()
        end)
        
        Option.MouseLeave:Connect(function()
            CreateTween(Option, {BackgroundTransparency = 0.9}, 0.2):Play()
        end)
        
        -- Option selection
        Option.MouseButton1Click:Connect(function()
            dropdown.Value = optionText
            SelectedValue.Text = optionText
            
            -- Animate option selection
            CreateTween(Option, {BackgroundTransparency = 0.7}, 0.1):Play()
            wait(0.1)
            CreateTween(Option, {BackgroundTransparency = 0.9}, 0.1):Play()
            
            -- Close dropdown
            dropdown:Toggle()
            
            if callback then
                callback(optionText)
            end
        end)
        
        return Option
    end
    
    -- Create all option buttons
    for _, option in ipairs(options) do
        CreateOption(option)
    end
    
    -- Toggle dropdown function
    function dropdown:Toggle()
        dropdown.Open = not dropdown.Open
        
        -- Calculate total height for options
        local optionsHeight = #options * 35 - 5
        
        -- Animate container
        CreateTween(DropdownContainer, {
            Size = dropdown.Open 
                and UDim2.new(1, 0, 0, 45 + optionsHeight)
                or UDim2.new(1, 0, 0, 35)
        }, 0.3):Play()
        
        -- Animate arrow
        CreateTween(Arrow, {
            Rotation = dropdown.Open and 180 or 0
        }, 0.3):Play()
    end
    
    -- Header click handling
    Header.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dropdown:Toggle()
        end
    end)
    
    -- Add hover effect to header
    Header.MouseEnter:Connect(function()
        CreateTween(Header, {BackgroundTransparency = 0.7}, 0.2):Play()
    end)
    
    Header.MouseLeave:Connect(function()
        CreateTween(Header, {BackgroundTransparency = 0.8}, 0.2):Play()
    end)
    
    -- Add search functionality
    local SearchBox = Instance.new("TextBox")
    SearchBox.Name = "SearchBox"
    SearchBox.Size = UDim2.new(1, -20, 0, 25)
    SearchBox.Position = UDim2.new(0, 10, 0, 40)
    SearchBox.BackgroundColor3 = THEME.Secondary
    SearchBox.BackgroundTransparency = 0.8
    SearchBox.BorderSizePixel = 0
    SearchBox.PlaceholderText = "Search..."
    SearchBox.Text = ""
    SearchBox.TextColor3 = THEME.Text
    SearchBox.PlaceholderColor3 = Color3.fromRGB(150, 150, 150)
    SearchBox.TextSize = 14
    SearchBox.Font = Enum.Font.Gotham
    SearchBox.Visible = false
    SearchBox.Parent = DropdownContainer
    
    local SearchCorner = Instance.new("UICorner")
    SearchCorner.CornerRadius = UDim.new(0, 4)
    SearchCorner.Parent = SearchBox
    
    -- Search functionality
    SearchBox:GetPropertyChangedSignal("Text"):Connect(function()
        local searchText = SearchBox.Text:lower()
        for _, option in ipairs(OptionsContainer:GetChildren()) do
            if option:IsA("TextButton") then
                option.Visible = option.Text:lower():find(searchText, 1, true) ~= nil
            end
        end
    end)
    
    -- Add to library elements
    table.insert(self.Elements, dropdown)
    
    return dropdown
end

-- Section Component
function Solar4:CreateSection(text)
    local SectionLabel = Instance.new("TextLabel")
    SectionLabel.Name = "SectionLabel"
    SectionLabel.Size = UDim2.new(1, -20, 0, 30)
    SectionLabel.Position = UDim2.new(0, 10, 0, 0)
    SectionLabel.BackgroundTransparency = 1
    SectionLabel.Text = text
    SectionLabel.TextColor3 = THEME.Primary
    SectionLabel.TextSize = 16
    SectionLabel.Font = Enum.Font.GothamBold
    SectionLabel.TextXAlignment = Enum.TextXAlignment.Left
    SectionLabel.Parent = self.Container
    
    -- Add separator line
    local Separator = Instance.new("Frame")
    Separator.Name = "Separator"
    Separator.Size = UDim2.new(1, -20, 0, 1)
    Separator.Position = UDim2.new(0, 10, 0, 25)
    Separator.BackgroundColor3 = THEME.Primary
    Separator.BackgroundTransparency = 0.8
    Separator.BorderSizePixel = 0
    Separator.Parent = SectionLabel
    
    return SectionLabel
end

-- Label Component
function Solar4:CreateLabel(text)
    local Label = Instance.new("TextLabel")
    Label.Name = "Label"
    Label.Size = UDim2.new(1, -20, 0, 25)
    Label.BackgroundTransparency = 1
    Label.Text = text
    Label.TextColor3 = THEME.Text
    Label.TextSize = 14
    Label.Font = Enum.Font.Gotham
    Label.TextXAlignment = Enum.TextXAlignment.Left
    Label.Parent = self.Container
    
    return Label
end
-- Notification System
local NotificationSystem = {}
NotificationSystem.__index = NotificationSystem

function NotificationSystem.new()
    local screen = Instance.new("ScreenGui")
    screen.Name = "Solar4Notifications"
    screen.ResetOnSpawn = false
    
    -- Container for notifications
    local container = Instance.new("Frame")
    container.Name = "NotificationContainer"
    container.Size = UDim2.new(0, 300, 1, 0)
    container.Position = UDim2.new(1, -310, 0, 0)
    container.BackgroundTransparency = 1
    container.Parent = screen
    
    local listLayout = Instance.new("UIListLayout")
    listLayout.Padding = UDim.new(0, 10)
    listLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
    listLayout.VerticalAlignment = Enum.VerticalAlignment.Bottom
    listLayout.Parent = container
    
    -- Parent the ScreenGui
    if RunService:IsStudio() then
        screen.Parent = game.Players.LocalPlayer.PlayerGui
    else
        screen.Parent = game.CoreGui
    end
    
    return setmetatable({
        Screen = screen,
        Container = container
    }, NotificationSystem)
end

function NotificationSystem:Notify(title, message, duration, type)
    duration = duration or 5
    type = type or "Info" -- Info, Success, Warning, Error
    
    -- Color schemes for different types
    local colors = {
        Info = THEME.Primary,
        Success = Color3.fromRGB(46, 204, 113),
        Warning = Color3.fromRGB(241, 196, 15),
        Error = Color3.fromRGB(231, 76, 60)
    }
    
    -- Create notification frame
    local notification = Instance.new("Frame")
    notification.Name = "Notification"
    notification.Size = UDim2.new(0, 280, 0, 80)
    notification.BackgroundColor3 = THEME.Background
    notification.BorderSizePixel = 0
    notification.Position = UDim2.new(0, 0, 0, 0)
    notification.Parent = self.Container
    
    -- Add corner radius
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 8)
    corner.Parent = notification
    
    -- Add glow effect
    local glow = Instance.new("ImageLabel")
    glow.Name = "Glow"
    glow.BackgroundTransparency = 1
    glow.Position = UDim2.new(0, -15, 0, -15)
    glow.Size = UDim2.new(1, 30, 1, 30)
    glow.Image = "rbxassetid://7603818383"
    glow.ImageColor3 = colors[type]
    glow.ImageTransparency = 0.8
    glow.Parent = notification
    
    -- Title
    local titleLabel = Instance.new("TextLabel")
    titleLabel.Name = "Title"
    titleLabel.Size = UDim2.new(1, -20, 0, 30)
    titleLabel.Position = UDim2.new(0, 10, 0, 5)
    titleLabel.BackgroundTransparency = 1
    titleLabel.Text = title
    titleLabel.TextColor3 = colors[type]
    titleLabel.TextSize = 16
    titleLabel.Font = Enum.Font.GothamBold
    titleLabel.TextXAlignment = Enum.TextXAlignment.Left
    titleLabel.Parent = notification
    
    -- Message
    local messageLabel = Instance.new("TextLabel")
    messageLabel.Name = "Message"
    messageLabel.Size = UDim2.new(1, -20, 0, 40)
    messageLabel.Position = UDim2.new(0, 10, 0, 35)
    messageLabel.BackgroundTransparency = 1
    messageLabel.Text = message
    messageLabel.TextColor3 = THEME.Text
    messageLabel.TextSize = 14
    messageLabel.Font = Enum.Font.Gotham
    messageLabel.TextXAlignment = Enum.TextXAlignment.Left
    messageLabel.TextWrapped = true
    messageLabel.Parent = notification
    
    -- Progress bar
    local progressBar = Instance.new("Frame")
    progressBar.Name = "ProgressBar"
    progressBar.Size = UDim2.new(1, 0, 0, 2)
    progressBar.Position = UDim2.new(0, 0, 1, -2)
    progressBar.BackgroundColor3 = colors[type]
    progressBar.BorderSizePixel = 0
    progressBar.Parent = notification
    
    -- Animate in
    notification.Position = UDim2.new(1, 0, 0, 0)
    CreateTween(notification, {Position = UDim2.new(0, 0, 0, 0)}, 0.3, Enum.EasingStyle.Back):Play()
    
    -- Animate progress bar
    CreateTween(progressBar, {Size = UDim2.new(0, 0, 0, 2)}, duration):Play()
    
    -- Remove after duration
    game:GetService("Debris"):AddItem(notification, duration)
    
    -- Animate out
    delay(duration - 0.3, function()
        if notification and notification.Parent then
            CreateTween(notification, {Position = UDim2.new(1, 0, 0, 0)}, 0.3):Play()
        end
    end)
end

-- Injection Code
local InjectionCode = [[
-- Solar4 Library Loader
local function LoadSolar4()
    local success, Solar4 = pcall(function()
        return loadstring(game:HttpGet('YOUR_RAW_GITHUB_LINK_HERE'))()
    end)
    
    if not success then
        warn("Failed to load Solar4 Library:", Solar4)
        return
    end
    
    -- Create main window
    local Window = Solar4.new()
    
    -- Create notification system
    local Notifications = NotificationSystem.new()
    
    -- Example usage:
    Notifications:Notify(
        "Solar4 Loaded!", 
        "Library has been successfully injected.", 
        5, 
        "Success"
    )
    
    return Window, Notifications
end

-- Load the library
local Window, Notifications = LoadSolar4()

-- Example usage of all components:
local section1 = Window:CreateSection("Controls")

local toggle = Window:CreateToggle("Toggle Example", function(value)
    print("Toggle:", value)
end)

local switch = Window:CreateSwitch("Switch Example", function(value)
    print("Switch:", value)
end)

local slider = Window:CreateSlider("Slider Example", 0, 100, 50, function(value)
    print("Slider:", value)
end)

local dropdown = Window:CreateDropdown("Dropdown Example", {
    "Option 1",
    "Option 2",
    "Option 3"
}, "Option 1", function(value)
    print("Dropdown:", value)
end)

local button = Window:CreateButton("Button Example", function()
    print("Button clicked!")
    Notifications:Notify("Button Clicked", "You clicked the example button!", 3, "Info")
end)
]]

-- Minify the injection code
local function MinifyCode(code)
    -- Remove comments
    code = code:gsub("%-%-[^\n]*", "")
    
    -- Remove unnecessary whitespace
    code = code:gsub("[ \t\n]+", " ")
    
    return code
end

-- Function to get the minified injection code
function Solar4:GetInjectionCode()
    return MinifyCode(InjectionCode)
end

-- Additional utility functions for the library
function Solar4:Destroy()
    if self.ScreenGui then
        self.ScreenGui:Destroy()
    end
end

function Solar4:Toggle()
    if self.MainFrame then
        self.MainFrame.Visible = not self.MainFrame.Visible
    end
end

function Solar4:SetTheme(newTheme)
    for key, value in pairs(newTheme) do
        if THEME[key] then
            THEME[key] = value
        end
    end
    
    -- Update all existing elements with new theme
    for _, element in ipairs(self.Elements) do
        if element.Type == "Toggle" then
            -- Update toggle colors
        elseif element.Type == "Button" then
            -- Update button colors
        -- Add more element type updates here
        end
    end
end

return Solar4
